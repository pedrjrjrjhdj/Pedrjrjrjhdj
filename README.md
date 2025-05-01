repeat task.wait() until game:IsLoaded()

local Players = game:GetService("Players")
local Lighting = game:GetService("Lighting")
local TeleportService = game:GetService("TeleportService")
local HttpService = game:GetService("HttpService")

local LocalPlayer = Players.LocalPlayer
local PlaceId = game.PlaceId
local visitedServers = {}

-- Detecta lua cheia pela textura da lua
local function IsFullMoon()
    for _, obj in ipairs(Lighting:GetChildren()) do
        if obj:IsA("Sky") and obj.MoonTexture then
            return string.find(obj.MoonTexture:lower(), "full") ~= nil
        end
    end
    return false
end

-- Teleporta para o templo
local function TeleportToTemple()
    local templeCFrame = CFrame.new(-5058, 314, -3156)
    local char = LocalPlayer.Character
    if char and char:FindFirstChild("HumanoidRootPart") then
        char.HumanoidRootPart.CFrame = templeCFrame
    end
end

-- Pega e entra em outro servidor aleatório não visitado
local function HopServer()
    local success, result = pcall(function()
        return HttpService:JSONDecode(game:HttpGet(
            "https://games.roblox.com/v1/games/" .. PlaceId .. "/servers/Public?sortOrder=Asc&limit=100"
        ))
    end)

    if success and result and result.data then
        for _, server in ipairs(result.data) do
            if server.playing < server.maxPlayers and not visitedServers[server.id] then
                visitedServers[server.id] = true
                TeleportService:TeleportToPlaceInstance(PlaceId, server.id, LocalPlayer)
                return
            end
        end
    end

    warn("Nenhum servidor válido encontrado. Tentando novamente...")
    task.wait(5)
    HopServer()
end

-- Loop principal
while true do
    task.wait(1)

    if IsFullMoon() then
        warn("Lua cheia detectada! Indo para o templo.")
        task.wait(1)
        TeleportToTemple()
        break
    else
        warn("Sem lua cheia. Trocando de servidor...")
        task.wait(2)
        HopServer()
        break -- necessário para não continuar o loop após teleport
    end
end
