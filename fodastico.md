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

local gui = Instance.new("ScreenGui", game.CoreGui)
gui.Name = "LucasMenu"
gui.ResetOnSpawn = false

local hacks = {
	Fly = false,
	Noclip = false,
	Aimbot = false
}

local function resetHacks()
	hacks.Fly = false
	hacks.Noclip = false
	hacks.Aimbot = false
	Humanoid.WalkSpeed = 16
end

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
frame.AnchorPoint = Vector2.new(0, 0.5)
frame.ClipsDescendants = true

Instance.new("UICorner", frame).CornerRadius = UDim.new(0, 12)
Instance.new("UIListLayout", frame).Padding = UDim.new(0, 8)

local title = Instance.new("TextLabel", frame)
title.Text = "🌟 Lucas Menu"
title.Size = UDim2.new(1, 0, 0, 50)
title.TextColor3 = Color3.new(1, 1, 1)
title.Font = Enum.Font.GothamBold
title.TextSize = 24
title.BackgroundTransparency = 1

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

-- WalkSpeed com caixa
local walkContainer = Instance.new("Frame", frame)
walkContainer.Size = UDim2.new(1, -20, 0, 40)
walkContainer.Position = UDim2.new(0, 10, 0, 0)
walkContainer.BackgroundTransparency = 1

local walkLabel = Instance.new("TextLabel", walkContainer)
walkLabel.Text = "🏃 WalkSpeed"
walkLabel.Size = UDim2.new(0.4, 0, 1, 0)
walkLabel.BackgroundTransparency = 1
walkLabel.TextColor3 = Color3.fromRGB(255, 255, 255)
walkLabel.Font = Enum.Font.GothamBold
walkLabel.TextSize = 16

local walkInput = Instance.new("TextBox", walkContainer)
walkInput.Size = UDim2.new(0.6, -10, 1, 0)
walkInput.Position = UDim2.new(0.4, 10, 0, 0)
walkInput.BackgroundColor3 = Color3.fromRGB(60, 60, 60)
walkInput.TextColor3 = Color3.new(1, 1, 1)
walkInput.Font = Enum.Font.Gotham
walkInput.TextSize = 16
walkInput.Text = tostring(Humanoid.WalkSpeed)
Instance.new("UICorner", walkInput).CornerRadius = UDim.new(0, 6)

walkInput.FocusLost:Connect(function()
	local val = tonumber(walkInput.Text)
	if val then
		Humanoid.WalkSpeed = math.clamp(val, 0, 100)
	else
		walkInput.Text = tostring(Humanoid.WalkSpeed)
	end
end)

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

-- Outros comandos diretos (sem toggle)
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
