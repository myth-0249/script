-- HBE (Hitbox Expansion) Script for Cloudy Executor

-- Anti-Cheat Blocker (Simplified)
local function disableAntiCheat()
    -- Destroy Local Anti-Cheat scripts
    for _,v in pairs(game:GetService("Players").LocalPlayer.PlayerScripts:GetChildren()) do
        if v:IsA("LocalScript") and v.Name:lower():find("anti") then
            v:Destroy()
        end
    end
end

-- Call the Anti-Cheat Blocker
disableAntiCheat()

print("[⚡] Anti-Cheat Blocker Activated Successfully")

-------------------------------------------------------------------
-- Show Label with "Made By @Myth0249" and Fade Out

local screenGui = Instance.new("ScreenGui", game.Players.LocalPlayer:WaitForChild("PlayerGui"))
screenGui.Name = "HBEToggleGui"

-- Create the label
local madeByLabel = Instance.new("TextLabel", screenGui)
madeByLabel.Size = UDim2.new(0, 400, 0, 50)
madeByLabel.Position = UDim2.new(0.5, -200, 0.4, -25)
madeByLabel.BackgroundTransparency = 1
madeByLabel.TextColor3 = Color3.fromRGB(255, 255, 0)
madeByLabel.Font = Enum.Font.SourceSansBold
madeByLabel.TextSize = 80
madeByLabel.Text = "Made By @Myth0249"
madeByLabel.TextStrokeTransparency = 0.5
madeByLabel.TextTransparency = 0

-- Fade out the label after 2 seconds
game:GetService("TweenService"):Create(madeByLabel, TweenInfo.new(5, Enum.EasingStyle.Linear, Enum.EasingDirection.Out), {TextTransparency = 1}):Play()

-- Wait for the fade to finish and then hide the label
wait(5)

madeByLabel:Destroy()  -- Remove the label after fading out

-------------------------------------------------------------------
-- HBE Script with Team Check + GUI Toggle + Hitbox Size Slider

local Players = game:GetService("Players")
local RunService = game:GetService("RunService")
local LocalPlayer = Players.LocalPlayer
local CHAR_PARENT = workspace

-- Variables
getgenv().HBE = true
local HITBOX_SIZE_VALUE = 15 -- Default Size
local HITBOX_COLOUR = Color3.fromRGB(255, 255, 0) -- Yellow
local DefaultSize = Vector3.new(2, 2, 1)

-- Character Parent
local function GetCharParent()
    repeat task.wait() until LocalPlayer.Character
    for _, char in pairs(workspace:GetDescendants()) do
        if char:IsA("Model") and char:FindFirstChild("Humanoid") and string.find(char.Name, LocalPlayer.Name) then
            return char.Parent
        end
    end
end
CHAR_PARENT = GetCharParent()

-- Hitbox Manager
local function UpdateHitboxes()
    for _, player in ipairs(Players:GetPlayers()) do
        if player ~= LocalPlayer then
            local char = CHAR_PARENT:FindFirstChild(player.Name)
            if char and char:FindFirstChild("HumanoidRootPart") then
                if getgenv().HBE and player.Team ~= LocalPlayer.Team then
                    local size = Vector3.new(HITBOX_SIZE_VALUE, HITBOX_SIZE_VALUE, HITBOX_SIZE_VALUE)
                    char.HumanoidRootPart.Size = size
                    char.HumanoidRootPart.Color = HITBOX_COLOUR
                    char.HumanoidRootPart.Transparency = 0.5
                    char.HumanoidRootPart.CanCollide = false
                else
                    char.HumanoidRootPart.Size = DefaultSize
                    char.HumanoidRootPart.Transparency = 1
                    char.HumanoidRootPart.CanCollide = false
                end
            end
        end
    end
end

RunService.RenderStepped:Connect(function()
    pcall(UpdateHitboxes)
end)

-------------------------------------------------------------------
-- GUI Setup

-- Toggle Button
local toggleButton = Instance.new("TextButton", screenGui)
toggleButton.Size = UDim2.new(0, 150, 0, 50)
toggleButton.Position = UDim2.new(0, 20, 0.8, 0)
toggleButton.BackgroundColor3 = Color3.fromRGB(50, 50, 50)
toggleButton.TextColor3 = Color3.fromRGB(255, 255, 0)
toggleButton.Font = Enum.Font.SourceSansBold
toggleButton.TextSize = 24
toggleButton.Text = "HBE: ON"
toggleButton.Active = true
toggleButton.Draggable = true

-- Slider Frame
local sliderFrame = Instance.new("Frame", screenGui)
sliderFrame.Size = UDim2.new(0, 200, 0, 50)
sliderFrame.Position = UDim2.new(0, 20, 0.75, 0)
sliderFrame.BackgroundColor3 = Color3.fromRGB(30, 30, 30)

local sliderBar = Instance.new("Frame", sliderFrame)
sliderBar.Size = UDim2.new(1, 0, 0.2, 0)
sliderBar.Position = UDim2.new(0, 0, 0.4, 0)
sliderBar.BackgroundColor3 = Color3.fromRGB(80, 80, 80)

local sliderButton = Instance.new("TextButton", sliderBar)
sliderButton.Size = UDim2.new(0, 20, 0, 20)
sliderButton.Position = UDim2.new(0.5, -10, 0.5, -10)
sliderButton.BackgroundColor3 = Color3.fromRGB(255, 255, 0)
sliderButton.Text = ""

local sliderValueText = Instance.new("TextLabel", sliderFrame)
sliderValueText.Size = UDim2.new(0, 200, 0, 25)
sliderValueText.Position = UDim2.new(0, 0, 0, -25)
sliderValueText.BackgroundTransparency = 1
sliderValueText.TextColor3 = Color3.fromRGB(255, 255, 0)
sliderValueText.Text = "Size: " .. HITBOX_SIZE_VALUE
sliderValueText.Font = Enum.Font.SourceSansBold
sliderValueText.TextSize = 20

-- Slider logic
local dragging = false

sliderButton.MouseButton1Down:Connect(function()
    dragging = true
end)

game:GetService("UserInputService").InputEnded:Connect(function(input)
    if input.UserInputType == Enum.UserInputType.MouseButton1 then
        dragging = false
    end
end)

RunService.RenderStepped:Connect(function()
    if dragging then
        local mouse = game.Players.LocalPlayer:GetMouse()
        local barPos = sliderBar.AbsolutePosition.X
        local barSize = sliderBar.AbsoluteSize.X
        local relative = math.clamp((mouse.X - barPos) / barSize, 0, 1)

        sliderButton.Position = UDim2.new(relative, -10, 0.5, -10)
        HITBOX_SIZE_VALUE = math.floor(5 + (relative * 45)) -- From 5 to 50
        sliderValueText.Text = "Size: " .. HITBOX_SIZE_VALUE
    end
end)

-- Toggle Button logic
toggleButton.MouseButton1Click:Connect(function()
    getgenv().HBE = not getgenv().HBE
    if getgenv().HBE then
        toggleButton.Text = "HBE: ON"
        toggleButton.BackgroundColor3 = Color3.fromRGB(50, 50, 50)
    else
        toggleButton.Text = "HBE: OFF"
        toggleButton.BackgroundColor3 = Color3.fromRGB(100, 0, 0)
    end
end)

print("[⚡] HBE Script with Anti-Cheat Blocker Activated Successfully")
