repeat task.wait() until game:IsLoaded()

local TeleportService = game:GetService("TeleportService")
local Lighting = game:GetService("Lighting")
local Players = game:GetService("Players")
local Player = Players.LocalPlayer
local PlaceId = game.PlaceId

-- Detecta se é lua cheia olhando o nome da textura da lua
local function IsFullMoon()
    for _, v in pairs(Lighting:GetChildren()) do
        if v:IsA("Sky") then
            if v.MoonTexture and typeof(v.MoonTexture) == "string" then
                return string.find(v.MoonTexture:lower(), "full") ~= nil
            end
        end
    end
    return false
end

-- Teleporta até o templo
local function TeleportToTemple()
    local templeCFrame = CFrame.new(-5058, 314, -3156)
    if Player.Character and Player.Character:FindFirstChild("HumanoidRootPart") then
        Player.Character.HumanoidRootPart.CFrame = templeCFrame
    end
end

-- Loop até achar lua cheia
while true do
    if IsFullMoon() then
        warn("Lua cheia detectada!")
        task.wait(2)
        TeleportToTemple()
        break
    else
        warn("Sem lua cheia... trocando de servidor.")
        task.wait(2)
        TeleportService:Teleport(PlaceId, Player)
        break -- Importante: quebra o loop, senão ele continua rodando após o teleport
    end
end
