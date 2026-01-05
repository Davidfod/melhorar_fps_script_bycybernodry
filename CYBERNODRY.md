local Players = game:GetService("Players")
local RunService = game:GetService("RunService")
local Lighting = game:GetService("Lighting")
local LocalPlayer = Players.LocalPlayer

-- // CONFIGURAÇÃO DE FORÇA
local Settings = {
    Active = false
}

-- // FUNÇÃO QUE DEIXA O JOGO LISO (FORÇA BRUTA)
local function ForceClean()
    -- Configurações de Engine
    settings().Rendering.QualityLevel = 1
    settings().Rendering.MeshPartDetailLevel = Enum.MeshPartDetailLevel.Level01
    
    -- Limpeza de Iluminação e Neblina
    Lighting.GlobalShadows = false
    Lighting.FogEnd = 9e9
    Lighting.CastShadows = false
    
    for _, v in pairs(Lighting:GetChildren()) do
        if v:IsA("PostProcessEffect") or v:IsA("BloomEffect") or v:IsA("BlurEffect") or v:IsA("SunRaysEffect") then
            v.Enabled = false
        end
    end

    -- Limpeza de Workspace (Onde o lag acontece)
    for _, obj in pairs(workspace:GetDescendants()) do
        if obj:IsA("BasePart") or obj:IsA("MeshPart") or obj:IsA("UnionOperation") then
            obj.Material = Enum.Material.SmoothPlastic
            obj.Reflectance = 0
            obj.CastShadow = false
        elseif obj:IsA("Decal") or obj:IsA("Texture") then
            obj:Destroy() -- Deleta a textura na força
        elseif obj:IsA("ParticleEmitter") or obj:IsA("Trail") or obj:IsA("Fire") or obj:IsA("Smoke") or obj:IsA("Sparkles") then
            obj.Enabled = false
        elseif obj:IsA("Mesh") or obj:IsA("SpecialMesh") then
            obj.TextureId = "" -- Remove a "pele" das meshes
        end
    end
    
    collectgarbage("collect") -- Limpa a memória RAM
end

-- // INTERFACE
local ScreenGui = Instance.new("ScreenGui")
ScreenGui.Name = "FPS_MAX_CYBER"
ScreenGui.Parent = game:GetService("CoreGui") or LocalPlayer:WaitForChild("PlayerGui")

local MainFrame = Instance.new("Frame", ScreenGui)
MainFrame.Size = UDim2.new(0, 220, 0, 150)
MainFrame.Position = UDim2.new(0.05, 0, 0.4, 0)
MainFrame.BackgroundColor3 = Color3.fromRGB(5, 5, 5)
MainFrame.Active = true
MainFrame.Draggable = true 
Instance.new("UICorner", MainFrame).CornerRadius = UDim.new(0, 12)

local UIStroke = Instance.new("UIStroke", MainFrame)
UIStroke.Thickness = 3
UIStroke.ApplyStrokeMode = Enum.ApplyStrokeMode.Border

local Title = Instance.new("TextLabel", MainFrame)
Title.Size = UDim2.new(1, 0, 0, 35)
Title.BackgroundTransparency = 1
Title.Text = "FORCE POTATO 100FPS"
Title.Font = Enum.Font.GothamBold
Title.TextColor3 = Color3.new(1,1,1)
Title.TextSize = 15

local Credits = Instance.new("TextLabel", MainFrame)
Credits.Size = UDim2.new(1, -10, 0, 20)
Credits.Position = UDim2.new(0, 5, 1, -22)
Credits.BackgroundTransparency = 1
Credits.Text = "Criado por CyberNoDry"
Credits.Font = Enum.Font.Code
Credits.TextColor3 = Color3.new(1,1,1)
Credits.TextSize = 11

local BoostBtn = Instance.new("TextButton", MainFrame)
BoostBtn.Size = UDim2.new(0, 180, 0, 45)
BoostBtn.Position = UDim2.new(0.5, -90, 0.4, 0)
BoostBtn.BackgroundColor3 = Color3.fromRGB(20, 20, 20)
BoostBtn.Text = "ATIVAR SUPER FPS"
BoostBtn.TextColor3 = Color3.fromRGB(255, 50, 50)
BoostBtn.Font = Enum.Font.GothamBold
Instance.new("UICorner", BoostBtn)

local MinBtn = Instance.new("TextButton", MainFrame)
MinBtn.Size = UDim2.new(0, 30, 0, 30)
MinBtn.Position = UDim2.new(1, -35, 0, 2)
MinBtn.BackgroundTransparency = 1
MinBtn.Text = "-"
MinBtn.TextColor3 = Color3.new(1,1,1)
MinBtn.TextSize = 25

-- Lógica de Minimizar
local isMin = false
MinBtn.MouseButton1Click:Connect(function()
    isMin = not isMin
    MainFrame:TweenSize(isMin and UDim2.new(0, 220, 0, 35) or UDim2.new(0, 220, 0, 150), "Out", "Quad", 0.2, true)
    BoostBtn.Visible, Credits.Visible = not isMin, not isMin
    MinBtn.Text = isMin and "+" or "-"
end)

-- // LOOP DE ATIVAÇÃO FORÇADA
BoostBtn.MouseButton1Click:Connect(function()
    Settings.Active = true
    BoostBtn.Text = "FPS OTIMIZADO!"
    BoostBtn.TextColor3 = Color3.fromRGB(50, 255, 100)
    
    -- Roda a limpeza uma vez imediatamente
    task.spawn(ForceClean)
    
    -- Mantém a limpeza rodando a cada 5 segundos para garantir que o lag não volte
    task.spawn(function()
        while Settings.Active do
            ForceClean()
            task.wait(5)
        end
    end)
end)

-- RGB Effect
RunService.RenderStepped:Connect(function()
    local color = Color3.fromHSV(tick() % 5 / 5, 1, 1)
    UIStroke.Color = color
    Title.TextColor3 = color
end)
