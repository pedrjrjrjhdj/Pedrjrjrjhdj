repeat wait() until game:IsLoaded()

local Players = game:GetService("Players")
local TeleportService = game:GetService("TeleportService")
local LocalPlayer = Players.LocalPlayer
local PlaceId = game.PlaceId

-- Nomes dos bosses desejados
local bossNames = {
    "rip_indra True Form",
    "Dough King"
}

-- Função para verificar se algum boss está vivo
local function bossAtivo()
    for _, inimigo in pairs(workspace.Enemies:GetChildren()) do
        for _, nome in pairs(bossNames) do
            if inimigo.Name == nome then
                warn("Boss encontrado:", nome)
                return true
            end
        end
    end
    return false
end

-- Loop: verifica boss e faz hop
while true do
    wait(5)
    if bossAtivo() then
        warn("Boss ativo no servidor! Parando o script.")
        break
    else
        warn("Nenhum boss encontrado. Trocando de servidor...")
        TeleportService:Teleport(PlaceId, LocalPlayer)
        wait(10) -- tempo antes do próximo hop
    end
end
