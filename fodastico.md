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
local Camera = workspace.CurrentCamera

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
frame.Size = UDim2.new(0, 300, 0, 660)
frame.Position = UDim2.new(0, 30, 0.3, 0)
frame.BackgroundColor3 = Color3.fromRGB(30, 30, 30)
frame.BorderSizePixel = 0
frame.BackgroundTransparency = 0
frame.AnchorPoint = Vector2.new(0, 0.5)
frame.ClipsDescendants = true

local uicorner = Instance.new("UICorner", frame)
uicorner.CornerRadius = UDim.new(0, 12)

local layout = Instance.new("UIListLayout", frame)
layout.Padding = UDim.new(0, 8)
layout.SortOrder = Enum.SortOrder.LayoutOrder

local title = Instance.new("TextLabel", frame)
title.Text = "🌟 Lucas Menu"
title.Size = UDim2.new(1, 0, 0, 50)
title.TextColor3 = Color3.new(1, 1, 1)
title.Font = Enum.Font.GothamBold
title.TextSize = 24
title.BackgroundTransparency = 1

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

-- Todos os botões do menu
createBtn("🏃 WalkSpeed (Set)", function()
	local val = tonumber(string.match(tostring(game:GetService("StarterGui"):PromptInput("WalkSpeed (0-100):")), "%d+")) or 16
	Humanoid.WalkSpeed = math.clamp(val, 0, 100)
end)

local flying = false
createBtn("🕊️ Fly (Toggle)", function()
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

createBtn("🧝 Teleportar para Jogador", function()
	local name = game:GetService("StarterGui"):PromptInput("Nome do jogador:")
	local p = Players:FindFirstChild(name)
	if p and p.Character and p.Character:FindFirstChild("HumanoidRootPart") then
		HRP.CFrame = p.Character.HumanoidRootPart.CFrame + Vector3.new(0, 5, 0)
	end
end)

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

createBtn("🎯 Aimbot (Toggle)", function()
	local enabled = true
	RS.RenderStepped:Connect(function()
		if not enabled then return end
		local closest
		local shortest = math.huge
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
end)

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
	local closest
	local shortest = math.huge
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
	gui:Destroy()
end)
