 repeat task.wait() until game:IsLoaded()

local Players = game:GetService("Players")
local Lighting = game:GetService("Lighting")
local TeleportService = game:GetService("TeleportService")
local HttpService = game:GetService("HttpService")

local LocalPlayer = Players.LocalPlayer
local PlaceId = game.PlaceId
local visitedServers = {}

-- Detecta lua cheia
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

-- Reexecuta o script após o hop
local function QueueNextServer()
    local scriptSource = [[
        repeat task.wait() until game:IsLoaded()

        local Players = game:GetService("Players")
        local Lighting = game:GetService("Lighting")
        local TeleportService = game:GetService("TeleportService")
        local HttpService = game:GetService("HttpService")

        local LocalPlayer = Players.LocalPlayer
        local PlaceId = game.PlaceId

        local function IsFullMoon()
            for _, obj in ipairs(Lighting:GetChildren()) do
                if obj:IsA("Sky") and obj.MoonTexture then
                    return string.find(obj.MoonTexture:lower(), "full") ~= nil
                end
            end
            return false
        end

        local function TeleportToTemple()
            local templeCFrame = CFrame.new(-5058, 314, -3156)
            local char = LocalPlayer.Character
            if char and char:FindFirstChild("HumanoidRootPart") then
                char.HumanoidRootPart.CFrame = templeCFrame
            end
        end

        local function HopServer()
            local success, result = pcall(function()
                return game:GetService("HttpService"):JSONDecode(game:HttpGet(
                    "https://games.roblox.com/v1/games/" .. PlaceId .. "/servers/Public?sortOrder=Asc&limit=100"
                ))
            end)

            if success and result and result.data then
                for _, server in ipairs(result.data) do
                    if server.playing < server.maxPlayers then
                        game:GetService("TeleportService"):TeleportToPlaceInstance(PlaceId, server.id, Players.LocalPlayer)
                        return
                    end
                end
            end

            warn("Sem servidores válidos. Tentando novamente...")
            task.wait(5)
            HopServer()
        end

        if IsFullMoon() then
            warn("Lua cheia encontrada!")
            task.wait(1)
            TeleportToTemple()
        else
            warn("Sem lua cheia. Pulando servidor...")
            task.wait(2)
            HopServer()
        end
    ]]

    queue_on_teleport(scriptSource)
end

-- Função de hop
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
                QueueNextServer()
                TeleportService:TeleportToPlaceInstance(PlaceId, server.id, LocalPlayer)
                return
            end
        end
    end

    warn("Nenhum servidor válido encontrado. Tentando de novo...")
    task.wait(5)
    HopServer()
end

-- Execução principal
if IsFullMoon() then
    warn("Lua cheia encontrada! Indo para o templo.")
    TeleportToTemple()
else
    warn("Sem lua cheia. Procurando outro servidor...")
    task.wait(2)
    HopServer()
end
