-- Server Hop com verificação de bosses pelos nomes exatos fornecidos

local HttpService = game:GetService("HttpService")
local TeleportService = game:GetService("TeleportService")
local Players = game:GetService("Players")
local LocalPlayer = Players.LocalPlayer
local PlaceId = game.PlaceId

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
    local servers = {}
    local url = "https://games.roblox.com/v1/games/"..PlaceId.."/servers/Public?limit=100"

    local success, response = pcall(function()
        return HttpService:JSONDecode(game:HttpGet(url))
    end)

    if success and response and response.data then
        for _, server in pairs(response.data) do
            if server.playing < server.maxPlayers then
                table.insert(servers, server.id)
            end
        end

        if #servers > 0 then
            TeleportService:TeleportToPlaceInstance(PlaceId, servers[math.random(1, #servers)], LocalPlayer)
        end
    end
end

-- Loop principal
while true do
    wait(5)
    if not bossIsAlive() then
        warn("Nenhum boss encontrado, fazendo hop.")
        serverHop()
        wait(15)
    else
        warn("Boss ativo encontrado. Parando o script.")
        break
    end
end
