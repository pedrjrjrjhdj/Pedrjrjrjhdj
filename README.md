repeat task.wait() until game:IsLoaded()

local TeleportService = game:GetService("TeleportService")
local Lighting = game:GetService("Lighting")
local Players = game:GetService("Players")
local RunService = game:GetService("RunService")
local Player = Players.LocalPlayer
local PlaceId = game.PlaceId

-- Detecta lua cheia pelo nome da textura
local function IsFullMoon()
    for _, v in pairs(Lighting:GetChildren()) do
        if v:IsA("Sky") and v.MoonTexture then
            return string.find(v.MoonTexture:lower(), "full") ~= nil
        end
    end
    return false
end

-- Verifica se a lua está começando (posição baixa no céu)
local function IsMoonJustStarting()
    local moonDirection = Lighting:GetMoonDirection() -- Retorna um Vector3 unitário
    -- A lua começa a subir quando Y (altura) está próximo de 0.1 ~ 0.3
    return moonDirection.Y > 0.1 and moonDirection.Y < 0.35
end

-- Teleporta para o templo
local function TeleportToTemple()
    local templeCFrame = CFrame.new(-5058, 314, -3156)
    if Player.Character and Player.Character:FindFirstChild("HumanoidRootPart") then
        Player.Character.HumanoidRootPart.CFrame = templeCFrame
    end
end

-- Loop até encontrar lua cheia começando
while true do
    if IsFullMoon() and IsMoonJustStarting() then
        warn("Lua cheia começando detectada!")
        task.wait(1)
        TeleportToTemple()
        break
    else
        warn("Não é lua cheia começando... trocando de servidor.")
        task.wait(2)
        TeleportService:Teleport(PlaceId, Player)
        break -- importante para não continuar o loop após o teleport
    end
end
