repeat wait() until game:IsLoaded()

local TeleportService = game:GetService("TeleportService")
local Players = game:GetService("Players")
local LocalPlayer = Players.LocalPlayer
local HttpService = game:GetService("HttpService")
local PlaceId = game.PlaceId

-- Lista de servidores já visitados
local serverHistory = {}
local hopDelay = 10 -- segundos entre hops

-- Função para salvar o ID do servidor atual
local function addServerToHistory()
    if not isfile("hop-history.json") then
        writefile("hop-history.json", "[]")
    end

    local data = HttpService:JSONDecode(readfile("hop-history.json"))
    table.insert(data, game.JobId)
    writefile("hop-history.json", HttpService:JSONEncode(data))
    serverHistory = data
end

-- Função para verificar se já visitou o servidor
local function alreadyVisited(jobId)
    for _, id in pairs(serverHistory) do
        if id == jobId then
            return true
        end
    end
    return false
end

-- Inicia histórico
addServerToHistory()

-- Função de server hop aleatório
local function serverHop()
    local servers = {}
    local success, result = pcall(function()
        return HttpService:JSONDecode(game:HttpGet("https://games.roblox.com/v1/games/"..PlaceId.."/servers/Public?limit=100"))
    end)

    if success then
        for _, server in pairs(result.data) do
            if server.playing < server.maxPlayers and not alreadyVisited(server.id) then
                table.insert(servers, server.id)
            end
        end

        if #servers > 0 then
            local chosen = servers[math.random(1, #servers)]
            print("Teleportando para novo servidor:", chosen)
            TeleportService:TeleportToPlaceInstance(PlaceId, chosen, LocalPlayer)
        else
            print("Nenhum servidor novo encontrado.")
        end
    else
        print("Erro ao buscar servidores:", result)
    end
end

-- Loop de hop contínuo (você pode trocar a condição abaixo)
while true do
    wait(hopDelay)

    -- Aqui você pode colocar qualquer verificação. Exemplo:
    local foundBoss = false
    for _, v in pairs(workspace.Enemies:GetChildren()) do
        if v.Name:find("rip_indra") or v.Name:find("Dough King") then
            foundBoss = true
            break
        end
    end

    if foundBoss then
        print("Boss encontrado, parando script.")
        break
    else
        print("Boss não encontrado, trocando de servidor.")
        serverHop()
    end
end
