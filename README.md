repeat task.wait() until game:IsLoaded()

local TeleportService = game:GetService("TeleportService")
local Lighting = game:GetService("Lighting")
local Players = game:GetService("Players")
local Player = Players.LocalPlayer
local PlaceId = game.PlaceId

-- Verifica se é lua cheia (pela textura da lua)
local function IsFullMoon()
    for _, v in pairs(Lighting:GetChildren()) do
        if v:IsA("Sky") and v.MoonTexture then
            return string.find(v.MoonTexture:lower(), "full") ~= nil
        end
    end
    return false
end

-- Verifica se a lua cheia está começando (posição baixa)
local function IsMoonJustStarting()
    local moonDir = Lighting:GetMoonDirection()
    return moonDir.Y > 0.1 and moonDir.Y < 0.35
end

-- Teleporta para o templo
local function TeleportToTemple()
    local templeCFrame = CFrame.new(-5058, 314, -3156)
    if Player.Character and Player.Character:FindFirstChild("HumanoidRootPart") then
        Player.Character.HumanoidRootPart.CFrame = templeCFrame
    end
end

-- Loop infinito: só para quando achar a lua cheia começando
while true do
    task.wait(1)
    
    if IsFullMoon() then
        if IsMoonJustStarting() then
            warn("Lua cheia começando detectada! Indo para o templo.")
            TeleportToTemple()
            break
        else
            warn("Lua cheia, mas já está alta. Trocando de servidor...")
        end
    else
        warn("Não é lua cheia. Trocando de servidor...")
    end

    task.wait(2)
    TeleportService:Teleport(PlaceId, Player)
    break -- permite que o script seja reiniciado no próximo servidor
end
