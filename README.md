-- Fly + WalkSpeed GUI (feito pra KRNL)

-- Espera o jogo carregar
if not game:IsLoaded() then
    game.Loaded:Wait()
end

local Players = game:GetService("Players")
local UIS = game:GetService("UserInputService")
local RS = game:GetService("RunService")
local player = Players.LocalPlayer
local character = player.Character or player.CharacterAdded:Wait()
local hrp = character:WaitForChild("HumanoidRootPart")
local humanoid = character:WaitForChild("Humanoid")

-- GUI Setup
local ScreenGui = Instance.new("ScreenGui", game.CoreGui)
ScreenGui.Name = "CheatGui"
ScreenGui.ResetOnSpawn = false

-- Fly Button
local flyButton = Instance.new("TextButton", ScreenGui)
flyButton.Size = UDim2.new(0, 100, 0, 40)
flyButton.Position = UDim2.new(0, 10, 0, 10)
flyButton.BackgroundColor3 = Color3.fromRGB(0, 170, 255)
flyButton.TextColor3 = Color3.new(1, 1, 1)
flyButton.Font = Enum.Font.SourceSansBold
flyButton.TextSize = 20
flyButton.Text = "Fly: OFF"

-- WalkSpeed Frame
local speedFrame = Instance.new("Frame", ScreenGui)
speedFrame.Size = UDim2.new(0, 200, 0, 100)
speedFrame.Position = UDim2.new(0, 10, 0, 60)
speedFrame.BackgroundColor3 = Color3.fromRGB(30, 30, 30)
speedFrame.BorderSizePixel = 0

-- Label
local speedLabel = Instance.new("TextLabel", speedFrame)
speedLabel.Size = UDim2.new(1, 0, 0, 30)
speedLabel.Position = UDim2.new(0, 0, 0, 0)
speedLabel.BackgroundTransparency = 1
speedLabel.Text = "WalkSpeed"
speedLabel.TextColor3 = Color3.new(1, 1, 1)
speedLabel.Font = Enum.Font.SourceSansBold
speedLabel.TextSize = 20

-- Slider
local slider = Instance.new("TextBox", speedFrame)
slider.Size = UDim2.new(1, -20, 0, 30)
slider.Position = UDim2.new(0, 10, 0, 35)
slider.BackgroundColor3 = Color3.fromRGB(50, 50, 50)
slider.Text = "16"
slider.TextColor3 = Color3.new(1, 1, 1)
slider.Font = Enum.Font.SourceSans
slider.TextSize = 18
slider.ClearTextOnFocus = false

-- OK Button
local applyButton = Instance.new("TextButton", speedFrame)
applyButton.Size = UDim2.new(1, -20, 0, 30)
applyButton.Position = UDim2.new(0, 10, 0, 70)
applyButton.BackgroundColor3 = Color3.fromRGB(0, 170, 0)
applyButton.TextColor3 = Color3.new(1, 1, 1)
applyButton.Font = Enum.Font.SourceSansBold
applyButton.TextSize = 18
applyButton.Text = "OK"

-- WALK SPEED FUNÇÃO
applyButton.MouseButton1Click:Connect(function()
	local val = tonumber(slider.Text)
	if val and val >= 0 and val <= 100 then
		humanoid.WalkSpeed = val
	else
		slider.Text = "Valor inválido"
	end
end)

-- FLY SYSTEM
local flying = false
local bv = Instance.new("BodyVelocity")
local bg = Instance.new("BodyGyro")
local speed = 50

bv.MaxForce = Vector3.new(1e9, 1e9, 1e9)
bv.Velocity = Vector3.zero
bv.P = 1250

bg.MaxTorque = Vector3.new(1e9, 1e9, 1e9)
bg.P = 3000
bg.CFrame = hrp.CFrame

local function FlyLoop()
	RS.RenderStepped:Connect(function()
		if flying then
			local camCF = workspace.CurrentCamera.CFrame
			local moveDir = Vector3.zero

			if UIS:IsKeyDown(Enum.KeyCode.W) then
				moveDir += camCF.LookVector
			end
			if UIS:IsKeyDown(Enum.KeyCode.S) then
				moveDir -= camCF.LookVector
			end
			if UIS:IsKeyDown(Enum.KeyCode.A) then
				moveDir -= camCF.RightVector
			end
			if UIS:IsKeyDown(Enum.KeyCode.D) then
				moveDir += camCF.RightVector
			end
			if UIS:IsKeyDown(Enum.KeyCode.Space) then
				moveDir += camCF.UpVector
			end
			if UIS:IsKeyDown(Enum.KeyCode.LeftControl) then
				moveDir -= camCF.UpVector
			end

			if moveDir.Magnitude > 0 then
				bv.Velocity = moveDir.Unit * speed
			else
				bv.Velocity = Vector3.zero
			end

			bv.Parent = hrp
			bg.CFrame = camCF
			bg.Parent = hrp
		end
	end)
end

FlyLoop()

flyButton.MouseButton1Click:Connect(function()
	flying = not flying
	if flying then
		flyButton.Text = "Fly: ON"
		bv.Parent = hrp
		bg.Parent = hrp
	else
		flyButton.Text = "Fly: OFF"
		bv:Destroy()
		bg:Destroy()
		bv = Instance.new("BodyVelocity")
		bg = Instance.new("BodyGyro")
		bv.MaxForce = Vector3.new(1e9, 1e9, 1e9)
		bv.Velocity = Vector3.zero
		bv.P = 1250
		bg.MaxTorque = Vector3.new(1e9, 1e9, 1e9)
		bg.P = 3000
		bg.CFrame = hrp.CFrame
	end
end)
