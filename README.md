-- Lucas Menu | Cheat GUI bonito e funcional (by ChatGPT)

if not game:IsLoaded() then game.Loaded:Wait() end

local Players = game:GetService("Players")
local LP = Players.LocalPlayer
local Character = LP.Character or LP.CharacterAdded:Wait()
local Humanoid = Character:WaitForChild("Humanoid")
local HRP = Character:WaitForChild("HumanoidRootPart")
local UIS = game:GetService("UserInputService")
local RS = game:GetService("RunService")
local TweenService = game:GetService("TweenService")

-- UI Setup
local gui = Instance.new("ScreenGui", game.CoreGui)
gui.Name = "LucasMenu"
gui.ResetOnSpawn = false

-- Intro animation
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

local frame = Instance.new("Frame", gui)
frame.Size = UDim2.new(0, 300, 0, 450)
frame.Position = UDim2.new(0, 30, 0.3, 0)
frame.BackgroundColor3 = Color3.fromRGB(30, 30, 30)
frame.BorderSizePixel = 0
frame.BackgroundTransparency = 0
frame.AnchorPoint = Vector2.new(0, 0.5)
frame.ClipsDescendants = true

-- Bordas arredondadas
local uicorner = Instance.new("UICorner", frame)
uicorner.CornerRadius = UDim.new(0, 12)

local layout = Instance.new("UIListLayout", frame)
layout.Padding = UDim.new(0, 8)
layout.SortOrder = Enum.SortOrder.LayoutOrder

-- Título
local title = Instance.new("TextLabel", frame)
title.Text = "🌟 Lucas Menu"
title.Size = UDim2.new(1, 0, 0, 50)
title.TextColor3 = Color3.new(1, 1, 1)
title.Font = Enum.Font.GothamBold
title.TextSize = 24
title.BackgroundTransparency = 1

-- Função para criar botões
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

	local corner = Instance.new("UICorner", btn)
	corner.CornerRadius = UDim.new(0, 8)

	btn.MouseButton1Click:Connect(callback)
	return btn
end

-- WalkSpeed
createBtn("🏃 WalkSpeed (Set)", function()
	local val = tonumber(string.match(tostring(game:GetService("StarterGui"):PromptInput("WalkSpeed (0-100):")), "%d+")) or 16
	Humanoid.WalkSpeed = math.clamp(val, 0, 100)
end)

-- Fly
local flying = false
createBtn("🗳️ Fly (Toggle)", function()
	flying = not flying
	local bv = Instance.new("BodyVelocity")
	local bg = Instance.new("BodyGyro")
	bv.MaxForce = Vector3.new(1e9, 1e9, 1e9)
	bg.MaxTorque = Vector3.new(1e9, 1e9, 1e9)
	bv.P = 1250
	bg.P = 3000
	bv.Parent = HRP
	bg.Parent = HRP

	local flyConn
	flyConn = RS.RenderStepped:Connect(function()
		if not flying then
			bv:Destroy()
			bg:Destroy()
			flyConn:Disconnect()
			return
		end
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
end)

-- Noclip
local noclip = false
createBtn("🚪 Noclip (Toggle)", function()
	noclip = not noclip
	RS.Stepped:Connect(function()
		if noclip and LP.Character then
			for _, p in pairs(LP.Character:GetDescendants()) do
				if p:IsA("BasePart") then
					p.CanCollide = false
				end
			end
		end
	end)
end)

-- Teleportar para jogador
createBtn("🧙 Teleportar para Jogador", function()
	local name = game:GetService("StarterGui"):PromptInput("Nome do jogador:")
	local p = Players:FindFirstChild(name)
	if p and p.Character and p.Character:FindFirstChild("HumanoidRootPart") then
		HRP.CFrame = p.Character.HumanoidRootPart.CFrame + Vector3.new(0, 5, 0)
	end
end)

-- Fling Player
createBtn("🌀 Fling Jogador", function()
	local name = game:GetService("StarterGui"):PromptInput("Nome do jogador para fling:")
	local p = Players:FindFirstChild(name)
	if p and p.Character and p.Character:FindFirstChild("HumanoidRootPart") then
		local fling = Instance.new("BodyThrust")
		fling.Force = Vector3.new(99999, 99999, 99999)
		fling.Location = p.Character.HumanoidRootPart.Position
		fling.Parent = p.Character.HumanoidRootPart
		wait(0.3)
		fling:Destroy()
	end
end)

-- Anti-Bot
createBtn("🤖 Anti-Bot", function()
	for _, p in pairs(Players:GetPlayers()) do
		if p ~= LP and #p.Name <= 3 then
			p:Kick("Bot detectado pelo Lucas Menu.")
		end
	end
end)
