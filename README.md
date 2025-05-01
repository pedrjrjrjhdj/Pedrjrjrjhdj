repeat wait() until game:IsLoaded()
local ReplicatedStorage = game:GetService("ReplicatedStorage")
local Players = game:GetService("Players")
local TeleportService = game:GetService("TeleportService")
local HttpService = game:GetService("HttpService")
local LocalPlayer = Players.LocalPlayer
local Workspace = game:GetService("Workspace")
local PlaceID = game.PlaceId

-- Função para verificar se o jogador tem a Holy Torch
local function hasHolyTorch()
    return LocalPlayer.Backpack:FindFirstChild("Holy Torch") or LocalPlayer.Character:FindFirstChild("Holy Torch")
end

-- Função para cavar nos túmulos com a Holy Torch
local function digTomb(tombstone)
    if tombstone and tombstone:IsA("BasePart") then
        LocalPlayer.Character.HumanoidRootPart.CFrame = tombstone.CFrame + Vector3.new(0, 3, 0)
        wait(0.5)
        ReplicatedStorage.Remotes.Dig:FireServer(tombstone)
    end
end

-- Função para verificar se o baú tem o Cálice Sagrado
local function openChest(chest)
    if chest and chest.Name == "Chest" then
        if chest:FindFirstChild("Holy Chalice") then
            chest:Activate()
            print("Cálice Sagrado encontrado e baú aberto!")
            return true
        end
    end
    return false
end

-- Função de Server Hop
local function hopServer()
    local success, servers = pcall(function()
        return HttpService:JSONDecode(game:HttpGet(
            "https://games.roblox.com/v1/games/" .. PlaceID .. "/servers/Public?sortOrder=Asc&limit=100"
        ))
    end)

    if success and servers.data then
        for _, server in pairs(servers.data) do
            if server.playing < server.maxPlayers then
                TeleportService:TeleportToPlaceInstance(PlaceID, server.id, LocalPlayer)
                wait(3)
                print("Trocando de servidor...")
                break
            end
        end
    end
end

-- Loop de busca pelo baú e server hop
while true do
    wait(2)

    -- Verifica se o jogador possui a Holy Torch
    if hasHolyTorch() then
        local foundCalyx = false

        -- Percorre os túmulos no Haunted Castle
        for _, tombstone in pairs(Workspace:GetDescendants()) do
            if tombstone:IsA("BasePart") and tombstone.Name == "Gravestone" then
                -- Tenta cavar o túmulo
                digTomb(tombstone)
                wait(2)

                -- Procura pelo baú próximo
                for _, chest in pairs(Workspace:GetDescendants()) do
                    if openChest(chest) then
                        foundCalyx = true
                        break
                    end
                end
            end
            if foundCalyx then break end
        end

        -- Se não encontrou o cálice, realiza Server Hop
        if not foundCalyx then
            print("Cálice não encontrado. Tentando outro servidor...")
            hopServer()
            wait(5)  -- Aguarda o servidor carregar
        else
            break  -- Sai do loop se o cálice foi encontrado
        end
    else
        print("Você não possui a Holy Torch.")
        break
    end
end
