-- Auto Farm Boss Namu
-- Ajuste as coordenadas e remotes conforme o jogo

local player = game.Players.LocalPlayer
local char = player.Character or player.CharacterAdded:Wait()

-- Função para atacar o boss
function attackNamu()
    local args = {
        [1] = "AttackBoss", -- pode variar: "Hit", "UseSkill", etc.
        [2] = "Namu" -- nome exato do boss
    }
    game:GetService("ReplicatedStorage").RemoteEvent:FireServer(unpack(args))
end

-- Auto farm loop
while true do
    wait(0.5)
    
    -- Teleporta para o boss Namu (substituir pelas coordenadas certas)
    char:MoveTo(Vector3.new(500, 15, -300))
    
    -- Ataca o boss
    attackNamu()
end
