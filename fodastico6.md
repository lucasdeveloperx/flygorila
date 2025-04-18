-- Lucas Menu | Cheat GUI Avançado

if not game:IsLoaded() then game.Loaded:Wait() end

local Players = game:GetService("Players")
local LP = Players.LocalPlayer
local Character = LP.Character or LP.CharacterAdded:Wait()
local Humanoid = Character:WaitForChild("Humanoid")
local HRP = Character:WaitForChild("HumanoidRootPart")
local UIS = game:GetService("UserInputService")
local RS = game:GetService("RunService")
local TweenService = game:GetService("TweenService")
local Camera = workspace.CurrentCamera
local TS = game:GetService("TweenService")

local gui = Instance.new("ScreenGui", game.CoreGui)
gui.Name = "LucasMenu"
gui.ResetOnSpawn = false

local hacks = {
    Fly = false,
    Noclip = false,
    Aimbot = false,
    Fling = false
}

local function resetHacks()
    hacks.Fly = false
    hacks.Noclip = false
    hacks.Aimbot = false
    hacks.Fling = false
    Humanoid.WalkSpeed = 16
end

-- Animação de introdução
local introFrame = Instance.new("Frame", gui)
introFrame.Size = UDim2.new(0, 0, 0, 0)
introFrame.Position = UDim2.new(0.5, 0, 0.5, 0)
introFrame.BackgroundColor3 = Color3.fromRGB(30, 30, 30)
introFrame.AnchorPoint = Vector2.new(0.5, 0.5)
introFrame.BackgroundTransparency = 1

local introText = Instance.new("TextLabel", introFrame)
introText.Size = UDim2.new(1, 0, 1, 0)
introText.Text = "🌟 Lucas Menu"
introText.TextColor3 = Color3.new(1, 1, 1)
introText.Font = Enum.Font.GothamBold
introText.TextSize = 24
introText.BackgroundTransparency = 1

TweenService:Create(introFrame, TweenInfo.new(1, Enum.EasingStyle.Bounce), {Size = UDim2.new(0, 300, 0, 450)}):Play()
wait(1.5)
introFrame:Destroy()

-- Frame principal
local frame = Instance.new("Frame", gui)
frame.Size = UDim2.new(0, 300, 0, 700)
frame.Position = UDim2.new(0, 30, 0.3, 0)
frame.BackgroundColor3 = Color3.fromRGB(30, 30, 30)
frame.BorderSizePixel = 0
frame.AnchorPoint = Vector2.new(0, 0.5)
frame.ClipsDescendants = true

Instance.new("UICorner", frame).CornerRadius = UDim.new(0, 12)
local layout = Instance.new("UIListLayout", frame)
layout.Padding = UDim.new(0, 8)

local title = Instance.new("TextLabel", frame)
title.Text = "🌟 Lucas Menu"
title.Size = UDim2.new(1, 0, 0, 50)
title.TextColor3 = Color3.new(1, 1, 1)
title.Font = Enum.Font.GothamBold
title.TextSize = 24
title.BackgroundTransparency = 1

-- Função para criar toggles
local function createToggle(text, stateTableKey, callback)
    local toggle = Instance.new("TextButton", frame)
    toggle.Size = UDim2.new(1, -20, 0, 40)
    toggle.Position = UDim2.new(0, 10, 0, 0)
    toggle.Font = Enum.Font.Gotham
    toggle.TextSize = 18
    toggle.AutoButtonColor = true
    toggle.BackgroundColor3 = hacks[stateTableKey] and Color3.fromRGB(0, 170, 127) or Color3.fromRGB(45, 45, 45)
    toggle.TextColor3 = Color3.fromRGB(255, 255, 255)
    toggle.Text = text .. (hacks[stateTableKey] and " ✅" or " ❌")
    Instance.new("UICorner", toggle).CornerRadius = UDim.new(0, 8)

    toggle.MouseButton1Click:Connect(function()
        hacks[stateTableKey] = not hacks[stateTableKey]
        toggle.BackgroundColor3 = hacks[stateTableKey] and Color3.fromRGB(0, 170, 127) or Color3.fromRGB(45, 45, 45)
        toggle.Text = text .. (hacks[stateTableKey] and " ✅" or " ❌")
        callback(hacks[stateTableKey])
    end)
end

-- Sistema de Fling automático corrigido
local function setupFling()
    local function flingPlayer(hit)
        if not hacks.Fling then return end
        
        local character = hit.Parent
        local humanoid = character:FindFirstChildOfClass("Humanoid")
        if not humanoid then return end
        
        local player = Players:GetPlayerFromCharacter(character)
        if player and player ~= LP then
            local rootPart = character:FindFirstChild("HumanoidRootPart")
            if rootPart then
                -- Cria uma explosão para lançar o jogador
                local explosion = Instance.new("Explosion")
                explosion.Position = rootPart.Position
                explosion.BlastPressure = 50000
                explosion.BlastRadius = 15
                explosion.DestroyJointRadiusPercent = 0
                explosion.ExplosionType = Enum.ExplosionType.NoCraters
                explosion.Parent = workspace
                
                -- Adiciona força adicional para lançar mais longe
                local bodyVelocity = Instance.new("BodyVelocity")
                bodyVelocity.Velocity = Vector3.new(
                    math.random(-2000, 2000),
                    math.random(3000, 5000),
                    math.random(-2000, 2000)
                )
                bodyVelocity.MaxForce = Vector3.new(math.huge, math.huge, math.huge)
                bodyVelocity.P = 10000
                bodyVelocity.Parent = rootPart
                
                game:GetService("Debris"):AddItem(explosion, 0.1)
                game:GetService("Debris"):AddItem(bodyVelocity, 0.2)
            end
        end
    end

    -- Conectar ao evento de toque
    local function connectTouched()
        for _, part in ipairs(Character:GetDescendants()) do
            if part:IsA("BasePart") then
                part.Touched:Connect(flingPlayer)
            end
        end
    end

    connectTouched()

    -- Reconectar se o personagem mudar
    LP.CharacterAdded:Connect(function(newChar)
        Character = newChar
        Humanoid = Character:WaitForChild("Humanoid")
        HRP = Character:WaitForChild("HumanoidRootPart")
        connectTouched()
    end)
end

setupFling()

-- Toggle para o Fling automático
createToggle("🌀 Fling Automático", "Fling", function(state)
    hacks.Fling = state
end)

-- Popup do WalkSpeed com efeito neôn
local walkSpeedPopup = Instance.new("Frame", gui)
walkSpeedPopup.Size = UDim2.new(0, 250, 0, 180)
walkSpeedPopup.Position = UDim2.new(0.5, 0, 0.5, 0)
walkSpeedPopup.AnchorPoint = Vector2.new(0.5, 0.5)
walkSpeedPopup.BackgroundColor3 = Color3.fromRGB(25, 25, 25)
walkSpeedPopup.BorderSizePixel = 0
walkSpeedPopup.Visible = false
walkSpeedPopup.ZIndex = 2
Instance.new("UICorner", walkSpeedPopup).CornerRadius = UDim.new(0, 12)

local neonBorder = Instance.new("UIStroke", walkSpeedPopup)
neonBorder.Color = Color3.fromRGB(0, 255, 255)
neonBorder.Thickness = 2
neonBorder.Transparency = 0.5
neonBorder.ApplyStrokeMode = Enum.ApplyStrokeMode.Border

local popupTitle = Instance.new("TextLabel", walkSpeedPopup)
popupTitle.Size = UDim2.new(1, 0, 0, 40)
popupTitle.Text = "🏃 WalkSpeed"
popupTitle.TextColor3 = Color3.fromRGB(0, 255, 255)
popupTitle.Font = Enum.Font.GothamBold
popupTitle.TextSize = 20
popupTitle.BackgroundTransparency = 1

local closeBtn = Instance.new("TextButton", walkSpeedPopup)
closeBtn.Size = UDim2.new(0, 30, 0, 30)
closeBtn.Position = UDim2.new(1, -35, 0, 5)
closeBtn.Text = "X"
closeBtn.Font = Enum.Font.GothamBold
closeBtn.TextSize = 18
closeBtn.TextColor3 = Color3.new(1, 1, 1)
closeBtn.BackgroundColor3 = Color3.fromRGB(255, 50, 50)
closeBtn.ZIndex = 3
Instance.new("UICorner", closeBtn).CornerRadius = UDim.new(0, 6)

local sliderTrack = Instance.new("Frame", walkSpeedPopup)
sliderTrack.Size = UDim2.new(0.8, 0, 0, 10)
sliderTrack.Position = UDim2.new(0.1, 0, 0.4, 0)
sliderTrack.BackgroundColor3 = Color3.fromRGB(40, 40, 40)
sliderTrack.BorderSizePixel = 0
Instance.new("UICorner", sliderTrack).CornerRadius = UDim.new(1, 0)

local sliderTrackNeon = Instance.new("UIStroke", sliderTrack)
sliderTrackNeon.Color = Color3.fromRGB(0, 200, 200)
sliderTrackNeon.Thickness = 2

local sliderFill = Instance.new("Frame", sliderTrack)
sliderFill.Size = UDim2.new(0.5, 0, 1, 0)
sliderFill.BackgroundColor3 = Color3.fromRGB(0, 170, 127)
sliderFill.BorderSizePixel = 0
Instance.new("UICorner", sliderFill).CornerRadius = UDim.new(1, 0)

local sliderBtn = Instance.new("TextButton", sliderTrack)
sliderBtn.Size = UDim2.new(0, 20, 0, 20)
sliderBtn.Position = UDim2.new(0.5, -10, 0.5, -10)
sliderBtn.BackgroundColor3 = Color3.new(1, 1, 1)
sliderBtn.Text = ""
sliderBtn.ZIndex = 2
Instance.new("UICorner", sliderBtn).CornerRadius = UDim.new(1, 0)

local sliderBtnNeon = Instance.new("UIStroke", sliderBtn)
sliderBtnNeon.Color = Color3.fromRGB(0, 255, 255)
sliderBtnNeon.Thickness = 2

local valueText = Instance.new("TextLabel", walkSpeedPopup)
valueText.Size = UDim2.new(1, 0, 0, 30)
valueText.Position = UDim2.new(0, 0, 0.6, 0)
valueText.Text = "Velocidade: "..tostring(Humanoid.WalkSpeed)
valueText.TextColor3 = Color3.fromRGB(0, 255, 255)
valueText.Font = Enum.Font.Gotham
valueText.TextSize = 18
valueText.BackgroundTransparency = 1

local applyBtn = Instance.new("TextButton", walkSpeedPopup)
applyBtn.Size = UDim2.new(0.6, 0, 0, 40)
applyBtn.Position = UDim2.new(0.2, 0, 0.8, -10)
applyBtn.Text = "Aplicar"
applyBtn.Font = Enum.Font.GothamBold
applyBtn.TextSize = 18
applyBtn.TextColor3 = Color3.new(1, 1, 1)
applyBtn.BackgroundColor3 = Color3.fromRGB(0, 170, 127)
Instance.new("UICorner", applyBtn).CornerRadius = UDim.new(0, 6)

local applyBtnNeon = Instance.new("UIStroke", applyBtn)
applyBtnNeon.Color = Color3.fromRGB(0, 255, 255)
applyBtnNeon.Thickness = 2

local function updateSlider(value)
    value = math.clamp(value, 0, 100)
    local percent = value / 100
    sliderFill.Size = UDim2.new(percent, 0, 1, 0)
    sliderBtn.Position = UDim2.new(percent, -10, 0.5, -10)
    valueText.Text = "Velocidade: "..tostring(math.floor(value))
    return value
end

local function setWalkSpeed(value)
    Humanoid.WalkSpeed = value
    updateSlider(value)
end

local function showWalkSpeedPopup()
    walkSpeedPopup.Visible = true
    updateSlider(Humanoid.WalkSpeed)
end

closeBtn.MouseButton1Click:Connect(function()
    walkSpeedPopup.Visible = false
end)

applyBtn.MouseButton1Click:Connect(function()
    walkSpeedPopup.Visible = false
    local speed = tonumber(string.match(valueText.Text, "%d+")) or 16
    Humanoid.WalkSpeed = speed
end)

local sliding = false
sliderBtn.MouseButton1Down:Connect(function()
    sliding = true
end)

UIS.InputEnded:Connect(function(input)
    if input.UserInputType == Enum.UserInputType.MouseButton1 then
        sliding = false
    end
end)

UIS.InputChanged:Connect(function(input)
    if sliding and input.UserInputType == Enum.UserInputType.MouseMovement then
        local x = (input.Position.X - sliderTrack.AbsolutePosition.X) / sliderTrack.AbsoluteSize.X
        local value = math.floor(x * 100)
        updateSlider(value)
    end
end)

-- Popup de seleção de jogadores
local teleportPopup = Instance.new("Frame", gui)
teleportPopup.Size = UDim2.new(0, 250, 0, 300)
teleportPopup.Position = UDim2.new(0.5, 0, 0.5, 0)
teleportPopup.AnchorPoint = Vector2.new(0.5, 0.5)
teleportPopup.BackgroundColor3 = Color3.fromRGB(25, 25, 25)
teleportPopup.BorderSizePixel = 0
teleportPopup.Visible = false
teleportPopup.ZIndex = 2
Instance.new("UICorner", teleportPopup).CornerRadius = UDim.new(0, 12)

local teleportNeonBorder = Instance.new("UIStroke", teleportPopup)
teleportNeonBorder.Color = Color3.fromRGB(255, 0, 255)
teleportNeonBorder.Thickness = 2
teleportNeonBorder.Transparency = 0.5

local teleportTitle = Instance.new("TextLabel", teleportPopup)
teleportTitle.Size = UDim2.new(1, 0, 0, 40)
teleportTitle.Text = "🧝 Teleportar para Jogador"
teleportTitle.TextColor3 = Color3.fromRGB(255, 0, 255)
teleportTitle.Font = Enum.Font.GothamBold
teleportTitle.TextSize = 20
teleportTitle.BackgroundTransparency = 1

local teleportCloseBtn = Instance.new("TextButton", teleportPopup)
teleportCloseBtn.Size = UDim2.new(0, 30, 0, 30)
teleportCloseBtn.Position = UDim2.new(1, -35, 0, 5)
teleportCloseBtn.Text = "X"
teleportCloseBtn.Font = Enum.Font.GothamBold
teleportCloseBtn.TextSize = 18
teleportCloseBtn.TextColor3 = Color3.new(1, 1, 1)
teleportCloseBtn.BackgroundColor3 = Color3.fromRGB(255, 50, 50)
teleportCloseBtn.ZIndex = 3
Instance.new("UICorner", teleportCloseBtn).CornerRadius = UDim.new(0, 6)

local playersList = Instance.new("ScrollingFrame", teleportPopup)
playersList.Size = UDim2.new(1, -20, 1, -100)
playersList.Position = UDim2.new(0, 10, 0, 50)
playersList.BackgroundTransparency = 1
playersList.BorderSizePixel = 0
playersList.ScrollBarThickness = 5
playersList.AutomaticCanvasSize = Enum.AutomaticSize.Y

local listLayout = Instance.new("UIListLayout", playersList)
listLayout.Padding = UDim.new(0, 5)

local teleportBtn = Instance.new("TextButton", teleportPopup)
teleportBtn.Size = UDim2.new(0.6, 0, 0, 40)
teleportBtn.Position = UDim2.new(0.2, 0, 1, -50)
teleportBtn.Text = "Teleportar"
teleportBtn.Font = Enum.Font.GothamBold
teleportBtn.TextSize = 18
teleportBtn.TextColor3 = Color3.new(1, 1, 1)
teleportBtn.BackgroundColor3 = Color3.fromRGB(170, 0, 170)
teleportBtn.Visible = false
Instance.new("UICorner", teleportBtn).CornerRadius = UDim.new(0, 6)

local teleportBtnNeon = Instance.new("UIStroke", teleportBtn)
teleportBtnNeon.Color = Color3.fromRGB(255, 0, 255)
teleportBtnNeon.Thickness = 2

local selectedPlayer = nil

local function updatePlayersList()
    for _, child in ipairs(playersList:GetChildren()) do
        if child:IsA("TextButton") then
            child:Destroy()
        end
    end
    
    for _, player in ipairs(Players:GetPlayers()) do
        if player ~= LP then
            local playerBtn = Instance.new("TextButton", playersList)
            playerBtn.Size = UDim2.new(1, 0, 0, 40)
            playerBtn.Text = "  " .. player.Name .. "  "  -- Espaços para visibilidade
            playerBtn.Font = Enum.Font.Gotham
            playerBtn.TextSize = 16
            playerBtn.TextColor3 = Color3.new(1, 1, 1)
            playerBtn.BackgroundColor3 = Color3.fromRGB(60, 60, 60)
            playerBtn.AutoButtonColor = true
            playerBtn.TextXAlignment = Enum.TextXAlignment.Left
            Instance.new("UICorner", playerBtn).CornerRadius = UDim.new(0, 6)
            
            local padding = Instance.new("UIPadding", playerBtn)
            padding.PaddingLeft = UDim.new(0, 10)
            
            playerBtn.MouseEnter:Connect(function()
                if playerBtn ~= selectedPlayer then
                    TS:Create(playerBtn, TweenInfo.new(0.2), {BackgroundColor3 = Color3.fromRGB(80, 80, 80)}):Play()
                end
            end)
            
            playerBtn.MouseLeave:Connect(function()
                if playerBtn ~= selectedPlayer then
                    TS:Create(playerBtn, TweenInfo.new(0.2), {BackgroundColor3 = Color3.fromRGB(60, 60, 60)}):Play()
                end
            end)
            
            playerBtn.MouseButton1Click:Connect(function()
                selectedPlayer = player
                teleportBtn.Visible = true
                for _, btn in ipairs(playersList:GetChildren()) do
                    if btn:IsA("TextButton") then
                        if btn == playerBtn then
                            TS:Create(btn, TweenInfo.new(0.2), {BackgroundColor3 = Color3.fromRGB(120, 0, 200)}):Play()
                        else
                            TS:Create(btn, TweenInfo.new(0.2), {BackgroundColor3 = Color3.fromRGB(60, 60, 60)}):Play()
                        end
                    end
                end
            end)
        end
    end
end

local function showTeleportPopup()
    teleportPopup.Visible = true
    updatePlayersList()
    selectedPlayer = nil
    teleportBtn.Visible = false
end

teleportCloseBtn.MouseButton1Click:Connect(function()
    teleportPopup.Visible = false
end)

teleportBtn.MouseButton1Click:Connect(function()
    if selectedPlayer and selectedPlayer.Character and selectedPlayer.Character:FindFirstChild("HumanoidRootPart") then
        HRP.CFrame = selectedPlayer.Character.HumanoidRootPart.CFrame + Vector3.new(0, 3, 0)
        teleportPopup.Visible = false
    end
end)

-- Botão WalkSpeed no menu principal
local walkSpeedBtn = Instance.new("TextButton", frame)
walkSpeedBtn.Size = UDim2.new(1, -20, 0, 40)
walkSpeedBtn.Position = UDim2.new(0, 10, 0, 0)
walkSpeedBtn.Text = "🏃 WalkSpeed"
walkSpeedBtn.Font = Enum.Font.GothamBold
walkSpeedBtn.TextSize = 18
walkSpeedBtn.TextColor3 = Color3.new(1, 1, 1)
walkSpeedBtn.BackgroundColor3 = Color3.fromRGB(45, 45, 45)
walkSpeedBtn.AutoButtonColor = true
Instance.new("UICorner", walkSpeedBtn).CornerRadius = UDim.new(0, 8)

walkSpeedBtn.MouseButton1Click:Connect(showWalkSpeedPopup)

-- Fly toggle
createToggle("🕊️ Fly", "Fly", function(state)
    if state then
        local bv = Instance.new("BodyVelocity")
        local bg = Instance.new("BodyGyro")
        bv.MaxForce = Vector3.new(1e9, 1e9, 1e9)
        bg.MaxTorque = Vector3.new(1e9, 1e9, 1e9)
        bv.P = 1250
        bg.P = 3000
        bv.Parent = HRP
        bg.Parent = HRP

        hacks.FlyConn = RS.RenderStepped:Connect(function()
            local cam = workspace.CurrentCamera.CFrame
            local dir = Vector3.zero
            if UIS:IsKeyDown(Enum.KeyCode.W) then dir += cam.LookVector end
            if UIS:IsKeyDown(Enum.KeyCode.S) then dir -= cam.LookVector end
            if UIS:IsKeyDown(Enum.KeyCode.A) then dir -= cam.RightVector end
            if UIS:IsKeyDown(Enum.KeyCode.D) then dir += cam.RightVector end
            if UIS:IsKeyDown(Enum.KeyCode.Space) then dir += cam.UpVector end
            if UIS:IsKeyDown(Enum.KeyCode.LeftControl) then dir -= cam.UpVector end
            bv.Velocity = dir.Magnitude > 0 and dir.Unit * 50 or Vector3.zero
            bg.CFrame = cam
        end)
    else
        if hacks.FlyConn then hacks.FlyConn:Disconnect() end
        for _, v in ipairs(HRP:GetChildren()) do
            if v:IsA("BodyVelocity") or v:IsA("BodyGyro") then v:Destroy() end
        end
    end
end)

-- Noclip toggle
createToggle("🚪 Noclip", "Noclip", function(state)
    if state then
        hacks.NoclipConn = RS.Stepped:Connect(function()
            if LP.Character then
                for _, p in pairs(LP.Character:GetDescendants()) do
                    if p:IsA("BasePart") then
                        p.CanCollide = false
                    end
                end
            end
        end)
    else
        if hacks.NoclipConn then hacks.NoclipConn:Disconnect() end
    end
end)

-- Aimbot toggle
createToggle("🎯 Aimbot", "Aimbot", function(state)
    if state then
        hacks.AimbotConn = RS.RenderStepped:Connect(function()
            local closest, shortest = nil, math.huge
            for _, p in pairs(Players:GetPlayers()) do
                if p ~= LP and p.Character and p.Character:FindFirstChild("Head") then
                    local pos, onScreen = Camera:WorldToViewportPoint(p.Character.Head.Position)
                    if onScreen then
                        local dist = (Vector2.new(pos.X, pos.Y) - UIS:GetMouseLocation()).Magnitude
                        if dist < shortest then
                            shortest = dist
                            closest = p
                        end
                    end
                end
            end
            if closest then
                Camera.CFrame = CFrame.new(Camera.CFrame.Position, closest.Character.Head.Position)
            end
        end)
    else
        if hacks.AimbotConn then hacks.AimbotConn:Disconnect() end
    end
end)

-- Outros comandos diretos
local function createBtn(text, callback)
    local btn = Instance.new("TextButton", frame)
    btn.Size = UDim2.new(1, -20, 0, 40)
    btn.Position = UDim2.new(0, 10, 0, 0)
    btn.BackgroundColor3 = Color3.fromRGB(45, 45, 45)
    btn.TextColor3 = Color3.fromRGB(255, 255, 255)
    btn.Font = Enum.Font.Gotham
    btn.TextSize = 18
    btn.Text = text
    btn.AutoButtonColor = true
    Instance.new("UICorner", btn).CornerRadius = UDim.new(0, 8)
    btn.MouseButton1Click:Connect(callback)
end

createBtn("🧝 Teleportar para Jogador", showTeleportPopup)

createBtn("🧱 Wallhack", function()
    for _, p in pairs(Players:GetPlayers()) do
        if p ~= LP and p.Character then
            for _, part in pairs(p.Character:GetChildren()) do
                if part:IsA("BasePart") then
                    local highlight = Instance.new("Highlight")
                    highlight.FillColor = Color3.fromRGB(255, 0, 0)
                    highlight.OutlineColor = Color3.new(1, 1, 1)
                    highlight.Adornee = part
                    highlight.Parent = part
                end
            end
        end
    end
end)

createBtn("⚡ Teleportar p/ Jogador Mais Próximo", function()
    local closest, shortest = nil, math.huge
    for _, p in pairs(Players:GetPlayers()) do
        if p ~= LP and p.Character and p.Character:FindFirstChild("HumanoidRootPart") then
            local dist = (HRP.Position - p.Character.HumanoidRootPart.Position).Magnitude
            if dist < shortest then
                shortest = dist
                closest = p
            end
        end
    end
    if closest then
        HRP.CFrame = closest.Character.HumanoidRootPart.CFrame + Vector3.new(0, 5, 0)
    end
end)

createBtn("🤖 Anti-Bot", function()
    for _, p in pairs(Players:GetPlayers()) do
        if p ~= LP and #p.Name <= 3 then
            p:Kick("Bot detectado pelo Lucas Menu.")
        end
    end
end)

createBtn("❌ Fechar Script", function()
    resetHacks()
    gui:Destroy()
end)

-- Atualizar lista de jogadores
Players.PlayerAdded:Connect(updatePlayersList)
Players.PlayerRemoving:Connect(updatePlayersList)
