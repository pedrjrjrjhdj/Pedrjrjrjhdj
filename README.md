local TeleportService = game:GetService("TeleportService")
local Players = game:GetService("Players")
local LocalPlayer = Players.LocalPlayer
local PlaceId = game.PlaceId
local workspace = game:GetService("Workspace")

-- Nomes exatos dos bosses
local bossNames = {
    "rip_indra True Form",
    "Dough King"
}

-- Função para normalizar texto (sem acento, minúsculo, sem espaços extras)
local function normalize(txt)
    return txt:lower():gsub("%s+", "") -- remove espaços e põe minúsculo
end

-- Verifica se algum boss está vivo
local function bossIsAlive()
    for _, v in pairs(workspace.Enemies:GetChildren()) do
        local enemyName = normalize(v.Name)
        for _, bossName in pairs(bossNames) do
            if enemyName:find(normalize(bossName)) then
                warn("Boss encontrado:", v.Name)
                return true
            end
        end
    end
    return false
end

-- Função para trocar de servidor
local function serverHop()
    local servers = game:GetService("TeleportService"):GetPlaceInstances(PlaceId)
    local validServers = {}

    for _, server in pairs(servers) do
        if server.playing < server.maxPlayers then
            table.insert(validServers, server.id)
        end
    end

    if #validServers > 0 then
        local serverToJoin = validServers[math.random(1, #validServers)]
        warn("Trocando para o servidor: " .. serverToJoin)
        TeleportService:TeleportToPlaceInstance(PlaceId, serverToJoin, LocalPlayer)
    else
        warn("Nenhum servidor disponível encontrado.")
    end
end

-- Loop principal
while true do
    wait(5)
    if not bossIsAlive() then
        warn("Nenhum boss encontrado, fazendo hop.")
        serverHop()
        wait(15)  -- Aguarda antes de tentar novamente
    else
        warn("Boss ativo encontrado. Parando o script.")
        break
    end
end
