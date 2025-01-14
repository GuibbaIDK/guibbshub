loadstring([[
-- Criação da GUI
local ScreenGui = Instance.new("ScreenGui")
local RollbackButton = Instance.new("TextButton")
local StopButton = Instance.new("TextButton")
local RejoinButton = Instance.new("TextButton")

ScreenGui.Parent = game.Players.LocalPlayer:WaitForChild("PlayerGui")
ScreenGui.Name = "RollbackGUI"

RollbackButton.Parent = ScreenGui
RollbackButton.Size = UDim2.new(0, 200, 0, 50)
RollbackButton.Position = UDim2.new(0.5, -100, 0.5, -90)
RollbackButton.Text = "Ativar Rollback"

StopButton.Parent = ScreenGui
StopButton.Size = UDim2.new(0, 200, 0, 50)
StopButton.Position = UDim2.new(0.5, -100, 0.5, -30)
StopButton.Text = "Desativar Rollback"

RejoinButton.Parent = ScreenGui
RejoinButton.Size = UDim2.new(0, 200, 0, 50)
RejoinButton.Position = UDim2.new(0.5, -100, 0.5, 30)
RejoinButton.Text = "Reentrar no Jogo"

local rollbackActive = false
local originalValues = {}

-- Função para ativar o rollback
RollbackButton.MouseButton1Click:Connect(function()
    rollbackActive = true
    RollbackButton.Text = "Rollback Ativado"
    print("Rollback ativado!")

    -- Salva os valores atuais como "estado inicial" (excluindo o tempo jogado)
    if game.Players.LocalPlayer:FindFirstChild("leaderstats") then
        for _, v in pairs(game.Players.LocalPlayer.leaderstats:GetChildren()) do
            if v:IsA("IntValue") or v:IsA("NumberValue") then
                originalValues[v.Name] = v.Value
                print("Valor salvo: " .. v.Name .. " = " .. v.Value)  -- Log de Debug
            end
        end
    else
        print("leaderstats não encontrado!")  -- Log de Debug
    end
end)

-- Função para desativar o rollback
StopButton.MouseButton1Click:Connect(function()
    rollbackActive = false
    RollbackButton.Text = "Ativar Rollback"
    print("Rollback desativado!")
end)

-- Função para interceptar alterações nos valores de moedas e impedir alterações
local mt = getrawmetatable(game)
local oldIndex = mt.__newindex

setreadonly(mt, false)
mt.__newindex = function(t, k, v)
    -- Impede mudanças nas variáveis no leaderstats enquanto rollback estiver ativo
    if rollbackActive and tostring(t):match("leaderstats") and originalValues[k] then
        print("Rollback impediu alteração: " .. tostring(k) .. " -> " .. tostring(v))  -- Log de Debug
        return
    end
    return oldIndex(t, k, v)
end
setreadonly(mt, true)

-- Função para reentrar no jogo
RejoinButton.MouseButton1Click:Connect(function()
    local TeleportService = game:GetService("TeleportService")
    local player = game.Players.LocalPlayer
    print("Reentrando no jogo...")  -- Log de Debug
    TeleportService:Teleport(game.PlaceId, player)
end)

print("Script de rollback com GUI e rejoin carregado com sucesso!")
]])()
