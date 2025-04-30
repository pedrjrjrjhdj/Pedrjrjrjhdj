-- Script Completo: Comprar Barco na Tiki, ir até o Leviatã e farmar

_G.FarmLeviathan = true

-- Coordenadas
local tikiBoatNPC = CFrame.new(-12581, 332, -9927) -- NPC na Ilha Tiki
local leviathanSpawn = Vector3.new(-13300, 0, -9600)

-- Ir até o NPC da Tiki
game.Players.LocalPlayer.Character.HumanoidRootPart.CFrame = tikiBoatNPC
wait(1)

-- Comprar o Leviathan Boat
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

-- Navegar até o Leviatã
spawn(function()
    while seat and seat.Parent and (seat.Position - leviathanSpawn).Magnitude > 50 do
        seat.Throttle = 1
        seat.Steer = 0
        seat.CFrame = CFrame.new(seat.Position, leviathanSpawn)
        wait(0.5)
    end
    seat.Throttle = 0
end)

-- Farmar Leviatã
spawn(function()
    while _G.FarmLeviathan do
        wait(2)
        local levi = workspace:FindFirstChild("Leviathan")
        if levi and levi:FindFirstChild("HumanoidRootPart") then
            game.Players.LocalPlayer.Character.HumanoidRootPart.CFrame = levi.HumanoidRootPart.CFrame * CFrame.new(0, 15, 0)
            repeat
                wait(0.3)
                game:GetService("VirtualUser"):Button1Down(Vector2.new(0,0), workspace.CurrentCamera.CFrame)
            until not levi or levi.Humanoid.Health <= 0 or not _G.FarmLeviathan
        end
    end
end)

-- Coletar Coração
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
end)-- Script Completo: Comprar Barco na Tiki, ir até o Leviatã e farmar

_G.FarmLeviathan = true

-- Coordenadas
local tikiBoatNPC = CFrame.new(-12581, 332, -9927) -- NPC na Ilha Tiki
local leviathanSpawn = Vector3.new(-13300, 0, -9600)

-- Ir até o NPC da Tiki
game.Players.LocalPlayer.Character.HumanoidRootPart.CFrame = tikiBoatNPC
wait(1)

-- Comprar o Leviathan Boat
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

-- Navegar até o Leviatã
spawn(function()
    while seat and seat.Parent and (seat.Position - leviathanSpawn).Magnitude > 50 do
        seat.Throttle = 1
        seat.Steer = 0
        seat.CFrame = CFrame.new(seat.Position, leviathanSpawn)
        wait(0.5)
    end
    seat.Throttle = 0
end)

-- Farmar Leviatã
spawn(function()
    while _G.FarmLeviathan do
        wait(2)
        local levi = workspace:FindFirstChild("Leviathan")
        if levi and levi:FindFirstChild("HumanoidRootPart") then
            game.Players.LocalPlayer.Character.HumanoidRootPart.CFrame = levi.HumanoidRootPart.CFrame * CFrame.new(0, 15, 0)
            repeat
                wait(0.3)
                game:GetService("VirtualUser"):Button1Down(Vector2.new(0,0), workspace.CurrentCamera.CFrame)
            until not levi or levi.Humanoid.Health <= 0 or not _G.FarmLeviathan
        end
    end
end)

-- Coletar Coração
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
