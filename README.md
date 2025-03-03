-- Criando o painel principal
local ScreenGui = Instance.new("ScreenGui")
local MainFrame = Instance.new("Frame")
local MinimizeButton = Instance.new("TextButton")
local TitleLabel = Instance.new("TextLabel")
local EspLineButton = Instance.new("TextButton")
local EspNickButton = Instance.new("TextButton")
local AntiLagButton = Instance.new("TextButton")
local AutoCLButton = Instance.new("TextButton")
local HitboxButton = Instance.new("TextButton")
local TeleportButton = Instance.new("TextButton")
local TeleportIlegalButton = Instance.new("TextButton") -- Botão Teleporte Ilegal
local TeleportPracaButton = Instance.new("TextButton") -- Novo botão Teleporte Praça

-- Configuração do painel principal
ScreenGui.Parent = game.Players.LocalPlayer:WaitForChild("PlayerGui")
MainFrame.Size = UDim2.new(0, 300, 0, 450)  -- Aumentei o tamanho para acomodar o novo botão
MainFrame.Position = UDim2.new(0.5, -150, 0.5, -225)  -- Ajustei para o novo tamanho
MainFrame.BackgroundColor3 = Color3.fromRGB(173, 216, 230) -- Azul Claro
MainFrame.Parent = ScreenGui

-- Título do painel
TitleLabel.Size = UDim2.new(1, 0, 0, 30)
TitleLabel.Position = UDim2.new(0, 0, 0, 0)
TitleLabel.BackgroundTransparency = 1
TitleLabel.Text = "AWAYS SIMPLES GRÁTIS"
TitleLabel.TextSize = 20
TitleLabel.TextColor3 = Color3.fromRGB(0, 0, 0)
TitleLabel.Parent = MainFrame

-- Botão de minimizar
MinimizeButton.Size = UDim2.new(0, 50, 0, 25)
MinimizeButton.Position = UDim2.new(1, -55, 0, 5)
MinimizeButton.BackgroundColor3 = Color3.fromRGB(255, 0, 0)
MinimizeButton.Text = "_"
MinimizeButton.Parent = MainFrame
MinimizeButton.MouseButton1Click:Connect(function()
    MainFrame.Visible = not MainFrame.Visible
end)

-- Função para alternar cor do botão
toggleButton = function(button, state)
    if state then
        button.BackgroundColor3 = Color3.fromRGB(0, 255, 0) -- Verde
    else
        button.BackgroundColor3 = Color3.fromRGB(255, 0, 0) -- Vermelho
    end
end

-- Criando os botões e funcionalidades
toggleFunctions = {}

local function createButton(name, pos, callback)
    local button = Instance.new("TextButton")
    button.Size = UDim2.new(0, 100, 0, 25)
    button.Position = UDim2.new(0, 10, 0, pos)
    button.BackgroundColor3 = Color3.fromRGB(255, 0, 0) -- Vermelho por padrão
    button.Text = name
    button.Parent = MainFrame
    local state = false
    button.MouseButton1Click:Connect(function()
        state = not state
        toggleButton(button, state)
        callback(state)
    end)
    return button
end

-- ESP Line
toggleFunctions["ESP Line"] = function(state)
    if state then
        for _, player in pairs(game.Players:GetPlayers()) do
            if player ~= game.Players.LocalPlayer and player.Character and player.Character:FindFirstChild("HumanoidRootPart") then
                local attachment1 = Instance.new("Attachment", player.Character.HumanoidRootPart)
                local attachment2 = Instance.new("Attachment", game.Players.LocalPlayer.Character.HumanoidRootPart)
                local beam = Instance.new("Beam")
                beam.Attachment0 = attachment1
                beam.Attachment1 = attachment2
                beam.Parent = player.Character
                beam.Color = ColorSequence.new(Color3.fromRGB(255, 0, 0))
            end
        end
    else
        for _, player in pairs(game.Players:GetPlayers()) do
            if player.Character then
                for _, obj in pairs(player.Character:GetChildren()) do
                    if obj:IsA("Beam") then obj:Destroy() end
                end
            end
        end
    end
end
EspLineButton = createButton("ESP Line", 40, toggleFunctions["ESP Line"])

-- ESP Nick
toggleFunctions["ESP Nick"] = function(state)
    if state then
        for _, player in pairs(game.Players:GetPlayers()) do
            if player.Character and not player.Character:FindFirstChild("BillboardGui") then
                local gui = Instance.new("BillboardGui", player.Character)
                gui.Size = UDim2.new(0, 100, 0, 50)
                gui.AlwaysOnTop = true
                local text = Instance.new("TextLabel", gui)
                text.Size = UDim2.new(1, 0, 1, 0)
                text.Text = player.Name
                text.TextColor3 = Color3.fromRGB(0, 0, 0) -- Nome Preto
                text.TextSize = 14 -- Nome Menor
                text.BackgroundTransparency = 1
            end
        end
    else
        for _, player in pairs(game.Players:GetPlayers()) do
            if player.Character and player.Character:FindFirstChild("BillboardGui") then
                player.Character.BillboardGui:Destroy()
            end
        end
    end
end
EspNickButton = createButton("ESP Nick", 70, toggleFunctions["ESP Nick"])

-- Auto CL
toggleFunctions["Auto CL"] = function(state)
    if state then
        game:GetService("RunService").Stepped:Connect(function()
            if game.Players.LocalPlayer.Character and game.Players.LocalPlayer.Character:FindFirstChild("Humanoid") then
                if game.Players.LocalPlayer.Character.Humanoid.Health == 0 then
                    game.Players.LocalPlayer:Kick("Removido pelo servidor.")
                end
            end
        end)
    end
end
AutoCLButton = createButton("Auto CL", 160, toggleFunctions["Auto CL"])

-- Hitbox
toggleFunctions["Hitbox"] = function(state)
    for _, player in pairs(game.Players:GetPlayers()) do
        if player.Character and player.Character:FindFirstChild("Head") then
            player.Character.Head.Size = state and Vector3.new(5, 5, 5) or Vector3.new(1, 1, 1)
        end
    end
end
HitboxButton = createButton("Hitbox", 190, toggleFunctions["Hitbox"])

-- Criando botão de teleporte
TeleportButton.Size = UDim2.new(0, 150, 0, 30)
TeleportButton.Position = UDim2.new(0, 75, 0, 250)
TeleportButton.BackgroundColor3 = Color3.fromRGB(0, 255, 0) -- Verde
TeleportButton.Text = "Teleporte Lavagem"
TeleportButton.Parent = MainFrame
TeleportButton.MouseButton1Click:Connect(function()
    local player = game.Players.LocalPlayer
    if player.Character and player.Character:FindFirstChild("HumanoidRootPart") then
        player.Character.HumanoidRootPart.CFrame = CFrame.new(19764.6, 69.6, 13152.5)
    end
end)

-- Criando botão de teleporte ilegal
TeleportIlegalButton.Size = UDim2.new(0, 150, 0, 30)
TeleportIlegalButton.Position = UDim2.new(0, 75, 0, 290)  -- Ajustei a posição para acomodar o novo botão
TeleportIlegalButton.BackgroundColor3 = Color3.fromRGB(255, 0, 0) -- Vermelho
TeleportIlegalButton.Text = "Teleporte Ilegal"
TeleportIlegalButton.Parent = MainFrame
TeleportIlegalButton.MouseButton1Click:Connect(function()
    local player = game.Players.LocalPlayer
    if player.Character and player.Character:FindFirstChild("HumanoidRootPart") then
        player.Character.HumanoidRootPart.CFrame = CFrame.new(12016.9, 30.0, 12810.3) -- Coordenadas do teleporte ilegal
    end
end)

-- Criando botão de teleporte para a Praça
TeleportPracaButton.Size = UDim2.new(0, 150, 0, 30)
TeleportPracaButton.Position = UDim2.new(0, 75, 0, 330)  -- Ajustei a posição para acomodar o novo botão
TeleportPracaButton.BackgroundColor3 = Color3.fromRGB(0, 0, 255) -- Azul
TeleportPracaButton.Text = "Teleporte Praça"
TeleportPracaButton.Parent = MainFrame
TeleportPracaButton.MouseButton1Click:Connect(function()
    local player = game.Players.LocalPlayer
    if player.Character and player.Character:FindFirstChild("HumanoidRootPart") then
        player.Character.HumanoidRootPart.CFrame = CFrame.new(-297.6, 4.8, 384.2) -- Coordenadas da Praça
    end
end)
