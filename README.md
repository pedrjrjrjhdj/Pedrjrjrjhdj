-- Lista de frutas míticas
local frutasMiticas = {
    "Leopard", "Dragon", "Spirit", "Dough", "Venom", "Control"
}

-- Checa se é fruta mítica
local function IsMitica(nome)
    for _, fruta in pairs(frutasMiticas) do
        if string.find(nome:lower(), fruta:lower()) then
            return true
        end
    end
    return false
end

-- Loop que detecta frutas no chão
while true do
    task.wait(1)
    for _, obj in pairs(game:GetService("Workspace"):GetChildren()) do
        if obj:IsA("Tool") and string.find(obj.Name:lower(), "fruit") then
            if IsMitica(obj.Name) then
                warn("Fruta MÍTICA encontrada: " .. obj.Name)
                local hrp = game.Players.LocalPlayer.Character:FindFirstChild("HumanoidRootPart")
                if hrp and obj:FindFirstChild("Handle") then
                    hrp.CFrame = obj.Handle.CFrame + Vector3.new(0, 2, 0)
                end
                return -- para o script após pegar
            else
                warn("Fruta comum detectada: " .. obj.Name .. " (ignorando)")
            end
        end
    end
end
