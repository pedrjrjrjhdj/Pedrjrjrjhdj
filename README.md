-- SCRIPT LEVIATÃ COM VOO ATÉ TIKI, BARCO NORMAL E FARM

_G.FarmLeviathan = true

local Players = game:GetService("Players")
local ReplicatedStorage = game:GetService("ReplicatedStorage")
local LocalPlayer = Players.LocalPlayer
local HRP = LocalPlayer.Character:WaitForChild("HumanoidRootPart")

-- Coordenadas
local tikiNPC = Vector3.new(-12581, 332, -9927)
local leviSpawn = Vector3.new(-13300, 0, -9600)

-- Função de voo suave
function flyTo(position)
    local speed = 120
    local bv = Instance.new("BodyVelocity")
    bv.Velocity = Vector3.zero
    bv.MaxForce = Vector3.new(1,1,1) * 1e6
    bv.P = 1e6
    bv.Parent = HRP

    while (HRP.Position - position).Magnitude > 10 do
        bv.Velocity = (position - HRP.Position).Unit * speed
        task.wait()
    end

    bv:Destroy()
    HRP.Position = position + Vector3.new(0, 5, 0)
end

-- Ir voando até NPC da Tiki
flyTo(tikiNPC)
wait(1)

-- Comprar barco Leviatã
local args = {"GetBoat", "Leviathan"}
ReplicatedStorage.Remotes.CommF_:InvokeServer(unpack(args))
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

-- Subir no barco
local seat = boat:FindFirstChildWhichIsA("VehicleSeat", true)
if seat then
    HRP.CFrame = seat.CFrame + Vector3.new(0, 5, 0)
    wait(1)
    firetouchinterest(HRP, seat, 0)
    firetouchinterest(HRP, seat, 1)
end

-- Barco vai até Leviatã
spawn(function()
    while seat and seat.Parent and (seat.Position - leviSpawn).Magnitude > 50 do
        seat.Throttle = 1
        seat.Steer = 0
        seat.CFrame = CFrame.new(seat.Position, leviSpawn)
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
            HRP.CFrame = levi.HumanoidRootPart.CFrame * CFrame.new(0, 15, 0)
            repeat
                wait(0.3)
                game:GetService("VirtualUser"):Button1Down(Vector2.new(0,0), workspace.CurrentCamera.CFrame)
            until not levi or levi.Humanoid.Health <= 0 or not _G.FarmLeviathan
        end
    end
end)

-- Coletar o Coração
spawn(function()
    while _G.FarmLeviathan do
        wait(2)
        for _, item in pairs(workspace:GetChildren()) do
            if item:IsA("Tool") and item.Name == "Leviathan Heart" then
                HRP.CFrame = item.Handle.CFrame
                firetouchinterest(HRP, item.Handle, 0)
                firetouchinterest(HRP, item.Handle, 1)
            end
        end
    end
end)
