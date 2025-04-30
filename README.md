-- SCRIPT COMPLETO: Comprar barco, ir até Leviatã, farmar e pegar o coração

_G.FarmLeviathan = true

-- Coordenadas
local boatNPCPos = CFrame.new(-13656, 332, -8267) -- NPC do barco
local leviathanIsland = Vector3.new(-13300, 0, -9600) -- Ilha do Leviatã

-- Ir até o NPC e comprar o barco
game.Players.LocalPlayer.Character.HumanoidRootPart.CFrame = boatNPCPos
wait(1)
local args = {
    [1] = "GetBoat",
    [2] = "Leviathan"
}
game:GetService("ReplicatedStorage").Remotes.CommF_:InvokeServer(unpack(args))
wait(2)

-- Achar o barco
local function getBoat()
    for _, obj in pairs(workspace.Boats:GetChildren()) do
        if obj.Name == "Leviathan Boat" and obj:FindFirstChildWhichIsA("VehicleSeat", true) then
            return obj
        end
    end
    return nil
end

local boat = nil
repeat boat = getBoat() wait(1) until boat

-- Entrar no barco
local seat = boat:FindFirstChildWhichIsA("VehicleSeat", true)
if seat then
    local char = game.Players.LocalPlayer.Character
    char.HumanoidRootPart.CFrame = seat.CFrame + Vector3.new(0, 5, 0)
    wait(1)
    firetouchinterest(char.HumanoidRootPart, seat, 0)
    firetouchinterest(char.HumanoidRootPart, seat, 1)
end

-- Navegar até ilha do Leviatã
spawn(function()
    while seat and seat.Parent and (seat.Position - leviathanIsland).Magnitude > 50 do
        seat.Throttle = 1
        seat.Steer = 0
        seat.CFrame = CFrame.new(seat.Position, leviathanIsland)
        wait(0.5)
    end
    seat.Throttle = 0
end)

-- Farmar Leviatã automaticamente
spawn(function()
    while _G.FarmLeviathan do
        wait(2)
        local levi = workspace:FindFirstChild("Leviathan")
        if levi and levi:FindFirstChild("HumanoidRootPart") then
            local hrp = levi.HumanoidRootPart
            game.Players.LocalPlayer.Character.HumanoidRootPart.CFrame = hrp.CFrame * CFrame.new(0, 15, 0)
            repeat
                wait(0.3)
                game:GetService("VirtualUser"):Button1Down(Vector2.new(0,0), workspace.CurrentCamera.CFrame)
            until not levi or levi.Humanoid.Health <= 0 or not _G.FarmLeviathan
        end
    end
end)

-- Coletar automaticamente o Leviathan Heart
spawn(function()
    while _G.FarmLeviathan do
        wait(2)
        for _, item in pairs(workspace:GetChildren()) do
            if item:IsA("Tool") and item.Name == "Leviathan Heart" then
                local char = game.Players.LocalPlayer.Character
                local hrp = char:FindFirstChild("HumanoidRootPart")
                if hrp then
                    hrp.CFrame = item.Handle.CFrame
                    firetouchinterest(hrp, item.Handle, 0)
                    firetouchinterest(hrp, item.Handle, 1)
                end
            end
        end
    end
end)
