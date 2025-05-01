repeat task.wait() until game:IsLoaded()

local TeleportService = game:GetService("TeleportService")
local HttpService = game:GetService("HttpService")
local Players = game:GetService("Players")
local Lighting = game:GetService("Lighting")
local LocalPlayer = Players.LocalPlayer

local PlaceId = game.PlaceId
local visitedServers = {}

-- Função para verificar se a lua é cheia
local function IsFullMoon()
    for _, obj in pairs(Lighting:GetChildren()) do
        if obj:IsA("Sky") and obj.MoonTexture then
            return string.find(obj.MoonTexture:lower(), "full") ~= nil
        end
    end
    return false
end

-- Verifica se a lua está no início (nascendo)
local function IsMoonJustStarting()
    local moonDir = Lighting:GetMoonDirection()
    return moonDir.Y > 0.1 and moonDir.Y < 0.35
end

-- Teleporta para o templo
local function TeleportToTemple()
    local templeCFrame = CFrame.new(-5058, 314, -3156)
    local char = LocalPlayer.Character
    if char and char:FindFirstChild("HumanoidRootPart") then
        char.HumanoidRootPart.CFrame = templeCFrame
    end
end

-- Teleporta para outro servidor aleatório
local function HopServer()
    local success, servers = pcall(function()
        return HttpService:JSONDecode(game:HttpGet(
            "https://games.roblox.com/v1/games/" .. PlaceId .. "/servers/Public?sortOrder=Asc&limit=100"
        ))
    end)

    if success and servers and servers.data then
        for _, server in pairs(servers.data) do
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

    if IsFullMoon() and IsMoonJustStarting() then
        warn("Lua cheia começando detectada! Indo para o templo.")
        task.wait(1)
        TeleportToTemple()
        break
    else
        warn("Lua não está começando. Trocando de servidor...")
        task.wait(2)
        HopServer()
        break
    end
end
