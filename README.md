local Players = game:GetService("Players")
local RunService = game:GetService("RunService")

local player = Players.LocalPlayer
local char = player.Character or player.CharacterAdded:Wait()
local backpack = player.Backpack

-- Criar Tool (BÍBLIA)
local bibleTool = Instance.new("Tool")
bibleTool.Name = "BÍBLIA SAGRADA"
bibleTool.RequiresHandle = false
bibleTool.Parent = backpack

-- Criar Bíblia 3D na Mão
local function createBibleModel()
    local bible = Instance.new("Part")
    bible.Size = Vector3.new(2, 3, 0.5)
    bible.Color = Color3.fromRGB(50, 50, 50)
    bible.Material = Enum.Material.SmoothPlastic
    bible.Name = "Bíblia"
    bible.CanCollide = false

    local mesh = Instance.new("SpecialMesh", bible)
    mesh.MeshType = Enum.MeshType.Brick
    mesh.Scale = Vector3.new(1, 1, 0.2)

    local weld = Instance.new("Weld", bible)
    weld.Part0 = char:FindFirstChild("RightHand") or char:FindFirstChild("Right Arm")
    weld.Part1 = bible
    weld.C0 = CFrame.new(0, 0, -1)

    return bible
end

-- Criar Título na Cabeça
local function createTitle()
    local billboard = Instance.new("BillboardGui")
    billboard.Size = UDim2.new(4, 0, 1, 0)
    billboard.StudsOffset = Vector3.new(0, 3, 0)
    billboard.Adornee = char.Head
    billboard.Parent = char.Head

    local textLabel = Instance.new("TextLabel", billboard)
    textLabel.Size = UDim2.new(1, 0, 1, 0)
    textLabel.BackgroundTransparency = 1
    textLabel.TextScaled = true
    textLabel.TextColor3 = Color3.fromRGB(255, 255, 0)
    textLabel.TextStrokeTransparency = 0
    textLabel.Text = "PROTEGIDO ⚔"
    textLabel.Font = Enum.Font.GothamBlack
    return billboard
end

-- Criar Áurea de Proteção
local function createAura()
    local aura = Instance.new("Part")
    aura.Size = Vector3.new(12, 12, 12) -- Aumentei um pouco mais a áurea
    aura.Shape = Enum.PartType.Ball
    aura.Color = Color3.fromRGB(255, 255, 255)
    aura.Material = Enum.Material.ForceField
    aura.Transparency = 0.5
    aura.Anchored = true
    aura.CanCollide = false
    aura.Parent = char

    -- Mover áurea com o personagem
    task.spawn(function()
        while aura.Parent do
            if char and char.PrimaryPart then
                aura.Position = char.PrimaryPart.Position
            end
            task.wait(0.1) -- Atualiza posição sem travar
        end
    end)

    return aura
end

-- Sistema de Dano na Áurea (a cada 0.5s para evitar lag)
local function applyAuraDamage(aura)
    task.spawn(function()
        while aura.Parent do
            for _, enemy in pairs(workspace:GetDescendants()) do
                if enemy:IsA("Model") and enemy ~= char and enemy:FindFirstChildOfClass("Humanoid") then
                    local humanoid = enemy:FindFirstChildOfClass("Humanoid")
                    local rootPart = enemy:FindFirstChild("HumanoidRootPart")

                    if humanoid and rootPart then
                        local distance = (rootPart.Position - char.PrimaryPart.Position).Magnitude
                        if distance <= 6 then -- Se estiver dentro da áurea, leva dano
                            humanoid:TakeDamage(20) -- Dano otimizado
                        end
                    end
                end
            end
            task.wait(0.5) -- Aplica dano a cada 0.5s (reduz lag)
        end
    end)
end

-- Auto Cura quando HP menor que 100
local function autoHeal()
    task.spawn(function()
        while char and char:FindFirstChildOfClass("Humanoid") do
            local humanoid = char:FindFirstChildOfClass("Humanoid")
            if humanoid.Health < 100 then
                humanoid.Health = humanoid.Health + 10 -- Cura aumentada
            end
            task.wait(1.5) -- Cura a cada 1.5 segundos (evita travamento)
        end
    end)
end

-- Criar Efeitos ao Equipar
bibleTool.Equipped:Connect(function()
    if not char or not char:FindFirstChild("HumanoidRootPart") then return end

    local bible = createBibleModel()
    bible.Parent = char

    local title = createTitle()
    title.Parent = char.Head

    local aura = createAura()
    applyAuraDamage(aura)
    autoHeal() -- Ativa a cura automática

    -- Mudar Animação de Movimento (Flutuação)
    local humanoid = char:FindFirstChildOfClass("Humanoid")
    if humanoid then
        humanoid.WalkSpeed = 8 -- Reduzi a velocidade para parecer flutuação
        humanoid.JumpPower = 60 -- Mais alto para parecer leve
    end
end)

-- Remover Efeitos ao Desequipar
bibleTool.Unequipped:Connect(function()
    if char then
        for _, obj in pairs(char:GetChildren()) do
            if obj:IsA("Part") and (obj.Name == "Bíblia" or obj.Shape == Enum.PartType.Ball) then
                obj:Destroy()
            end
            if obj:IsA("BillboardGui") then
                obj:Destroy()
            end
        end

        -- Restaurar Movimentação Normal
        local humanoid = char:FindFirstChildOfClass("Humanoid")
        if humanoid then
            humanoid.WalkSpeed = 16
            humanoid.JumpPower = 50
        end
    end
end)
