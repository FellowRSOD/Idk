-- SERVICES
local Players = game:GetService("Players")
local UIS = game:GetService("UserInputService")
local TweenService = game:GetService("TweenService")
local Lighting = game:GetService("Lighting")

local LP = Players.LocalPlayer

-- BLUR
local blur = Instance.new("BlurEffect")
blur.Size = 0
blur.Parent = Lighting

-- GUI
local gui = Instance.new("ScreenGui")
gui.Name = "HumMagKeyGUI"
gui.ResetOnSpawn = false
gui.Parent = LP:WaitForChild("PlayerGui")

-- MAIN BOX
local Main = Instance.new("Frame")
Main.Size = UDim2.fromScale(0.45, 0.25)
Main.Position = UDim2.fromScale(0.275, 0.35)
Main.BackgroundColor3 = Color3.fromRGB(18,18,18)
Main.Parent = gui
Main.Active = true

Instance.new("UICorner", Main).CornerRadius = UDim.new(0, 20)
local Stroke = Instance.new("UIStroke", Main)
Stroke.Thickness = 2
Stroke.Color = Color3.fromRGB(120,120,255)

-- TITLE
local Title = Instance.new("TextLabel")
Title.Size = UDim2.new(1,0,0,45)
Title.BackgroundTransparency = 1
Title.Text = "HumMag Scripts"
Title.Font = Enum.Font.GothamBold
Title.TextScaled = true
Title.TextColor3 = Color3.new(1,1,1)
Title.Parent = Main

-- KEY BOX
local KeyBox = Instance.new("TextBox")
KeyBox.Size = UDim2.fromScale(0.85,0.28)
KeyBox.Position = UDim2.fromScale(0.075,0.4)
KeyBox.PlaceholderText = "Enter Key"
KeyBox.Text = ""
KeyBox.Font = Enum.Font.Gotham
KeyBox.TextScaled = true
KeyBox.BackgroundColor3 = Color3.fromRGB(30,30,30)
KeyBox.TextColor3 = Color3.new(1,1,1)
KeyBox.Parent = Main
Instance.new("UICorner", KeyBox)

-- SUBMIT BUTTON
local Submit = Instance.new("TextButton")
Submit.Size = UDim2.fromScale(0.6,0.22)
Submit.Position = UDim2.fromScale(0.2,0.72)
Submit.Text = "UNLOCK"
Submit.Font = Enum.Font.GothamBold
Submit.TextScaled = true
Submit.BackgroundColor3 = Color3.fromRGB(50,50,90)
Submit.TextColor3 = Color3.new(1,1,1)
Submit.Parent = Main
Instance.new("UICorner", Submit)

-- ERROR TEXT
local Status = Instance.new("TextLabel")
Status.Size = UDim2.new(1,0,0,30)
Status.Position = UDim2.new(0,0,1,-30)
Status.BackgroundTransparency = 1
Status.Text = ""
Status.Font = Enum.Font.Gotham
Status.TextScaled = true
Status.TextColor3 = Color3.fromRGB(255,100,100)
Status.Parent = Main

-- BLUR IN
TweenService:Create(blur, TweenInfo.new(0.4), {Size = 20}):Play()

-- DRAG (MOBILE SAFE)
local dragging, dragStart, startPos
Main.InputBegan:Connect(function(i)
	if i.UserInputType == Enum.UserInputType.MouseButton1 or i.UserInputType == Enum.UserInputType.Touch then
		dragging = true
		dragStart = i.Position
		startPos = Main.Position
	end
end)

UIS.InputChanged:Connect(function(i)
	if dragging and (i.UserInputType == Enum.UserInputType.MouseMovement or i.UserInputType == Enum.UserInputType.Touch) then
		local delta = i.Position - dragStart
		Main.Position = startPos + UDim2.fromOffset(delta.X, delta.Y)
	end
end)

UIS.InputEnded:Connect(function()
	dragging = false
end)

-- KEY CHECK
local CORRECT_KEY = "HumMagScripts1"

Submit.MouseButton1Click:Connect(function()
	if KeyBox.Text == CORRECT_KEY then
		Status.TextColor3 = Color3.fromRGB(100,255,100)
		Status.Text = "Access Granted"

		-- UNLOCK ANIMATION
		TweenService:Create(Main, TweenInfo.new(0.3), {
			Size = UDim2.fromScale(0,0),
			Position = UDim2.fromScale(0.5,0.5)
		}):Play()

		TweenService:Create(blur, TweenInfo.new(0.4), {Size = 0}):Play()

		task.wait(0.35)
		gui:Destroy()

		-- 🔓 LOAD YOUR MAIN ESP UI HERE
		print("HumMag Scripts Loaded")

	else
		Status.TextColor3 = Color3.fromRGB(255,80,80)
		Status.Text = "Invalid Key"
	end
end)
