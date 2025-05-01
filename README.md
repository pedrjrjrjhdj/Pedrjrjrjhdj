repeat task.wait() until game:IsLoaded()

local Lighting = game:GetService("Lighting")

local function IsFullMoon()
    for _, obj in ipairs(Lighting:GetChildren()) do
        if obj:IsA("Sky") and obj.MoonTexture then
            return string.find(obj.MoonTexture:lower(), "full") ~= nil
        end
    end
    return false
end

if IsFullMoon() then
    warn("Lua cheia detectada! Indo para o templo.")
    local Players = game:GetService("Players")
    local Player = Players.LocalPlayer
    local templeCFrame = CFrame.new(-5058, 314, -3156)
    if Player.Character and Player.Character:FindFirstChild("HumanoidRootPart") then
        Player.Character.HumanoidRootPart.CFrame = templeCFrame
    end
else
    warn("Não é lua cheia! Troque de servidor manualmente.")
end
