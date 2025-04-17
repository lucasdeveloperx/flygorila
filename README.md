-- Fly com botão GUI (para testes com KRNL)
-- Feito para ser executado via injetor (KRNL, Synapse, etc.)

-- Espera o jogo carregar completamente
if not game:IsLoaded() then
    game.Loaded:Wait()
end

local Players = game:GetService("Players")
local player = Players.LocalPlayer
local character = player.Character or player.CharacterAdded:Wait()
local hrp = character:WaitForChild("HumanoidRootPart")

-- Cria GUI
local ScreenGui = Instance.new("ScreenGui")
ScreenGui.Name = "FlyTestGui"
ScreenGui.ResetOnSpawn = false
ScreenGui.Parent = game.CoreGui

local flyButton = Instance.new("TextButton")
flyButton.Name = "FlyButton"
flyButton.Size = UDim2.new(0, 100, 0, 40)
flyButton.Position = UDim2.new(0, 10, 0, 10)
flyButton.BackgroundColor3 = Color3.fromRGB(0, 170, 255)
flyButton.Text = "Fly: OFF"
flyButton.TextColor3 = Color3.new(1, 1, 1)
flyButton.Font = Enum.Font.SourceSansBold
flyButton.TextSize = 20
flyButton.Parent = ScreenGui

-- Variáveis de controle
local flying = false
local speed = 50
local UIS = game:GetService("UserInputService")
local RunService = game:GetService("RunService")

local bv = Instance.new("BodyVelocity")
bv.MaxForce = Vector3.new(1e9, 1e9, 1e9)
bv.Velocity = Vector3.zero
bv.P = 1250

local bg = Instance.new("BodyGyro")
bg.MaxTorque = Vector3.new(1e9, 1e9, 1e9)
bg.P = 3000
bg.CFrame = hrp.CFrame

-- Fly loop
local function FlyLoop()
    RunService.RenderStepped:Connect(function()
        if flying then
            local camCF = workspace.CurrentCamera.CFrame
            local moveDir = Vector3.zero

            if UIS:IsKeyDown(Enum.KeyCode.W) then
                moveDir = moveDir + camCF.LookVector
            end
            if UIS:IsKeyDown(Enum.KeyCode.S) then
                moveDir = moveDir - camCF.LookVector
            end
            if UIS:IsKeyDown(Enum.KeyCode.A) then
                moveDir = moveDir - camCF.RightVector
            end
            if UIS:IsKeyDown(Enum.KeyCode.D) then
                moveDir = moveDir + camCF.RightVector
            end
            if UIS:IsKeyDown(Enum.KeyCode.Space) then
                moveDir = moveDir + camCF.UpVector
            end
            if UIS:IsKeyDown(Enum.KeyCode.LeftControl) then
                moveDir = moveDir - camCF.UpVector
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

-- Botão ativa/desativa fly
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

        -- recriar os objetos pra reusar
        bv = Instance.new("BodyVelocity")
        bv.MaxForce = Vector3.new(1e9, 1e9, 1e9)
        bv.Velocity = Vector3.zero
        bv.P = 1250

        bg = Instance.new("BodyGyro")
        bg.MaxTorque = Vector3.new(1e9, 1e9, 1e9)
        bg.P = 3000
        bg.CFrame = hrp.CFrame
    end
end)
