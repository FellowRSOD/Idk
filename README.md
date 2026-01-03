-- SERVICES
local Players = game:GetService("Players")
local UIS = game:GetService("UserInputService")
local TweenService = game:GetService("TweenService")

local LP = Players.LocalPlayer

-- GUI
local gui = Instance.new("ScreenGui", LP.PlayerGui)
gui.Name = "BetterESP"
gui.ResetOnSpawn = false

-- Toggle Button
local Toggle = Instance.new("TextButton", gui)
Toggle.Size = UDim2.fromOffset(52,52)
Toggle.Position = UDim2.fromScale(0.02,0.55)
Toggle.Text = "ESP"
Toggle.Font = Enum.Font.GothamBold
Toggle.TextScaled = true
Toggle.BackgroundColor3 = Color3.fromRGB(30,30,30)
Toggle.TextColor3 = Color3.new(1,1,1)
Instance.new("UICorner", Toggle).CornerRadius = UDim.new(1,0)

-- Main Panel
local Main = Instance.new("Frame", gui)
Main.Size = UDim2.fromScale(0.45,0.45)
Main.Position = UDim2.fromScale(0.1,0.25)
Main.BackgroundColor3 = Color3.fromRGB(18,18,18)
Main.Visible = false
Main.Active = true
Instance.new("UICorner", Main).CornerRadius = UDim.new(0,20)

-- Stroke
local Stroke = Instance.new("UIStroke", Main)
Stroke.Thickness = 2
Stroke.Color = Color3.fromRGB(90,90,255)

-- Title
local Title = Instance.new("TextLabel", Main)
Title.Size = UDim2.new(1,0,0,50)
Title.BackgroundTransparency = 1
Title.Text = "ESP PANEL"
Title.Font = Enum.Font.GothamBold
Title.TextScaled = true
Title.TextColor3 = Color3.new(1,1,1)

-- Scroll
local Scroll = Instance.new("ScrollingFrame", Main)
Scroll.Position = UDim2.new(0,0,0,50)
Scroll.Size = UDim2.new(1,0,1,-50)
Scroll.CanvasSize = UDim2.fromScale(0,1)
Scroll.ScrollBarImageTransparency = 1

local Layout = Instance.new("UIListLayout", Scroll)
Layout.Padding = UDim.new(0,10)
Layout.HorizontalAlignment = Enum.HorizontalAlignment.Center

-- Button creator
local function MakeToggle(text)
	local b = Instance.new("TextButton", Scroll)
	b.Size = UDim2.fromScale(0.9,0.14)
	b.Text = text .. ": OFF"
	b.Font = Enum.Font.Gotham
	b.TextScaled = true
	b.BackgroundColor3 = Color3.fromRGB(35,35,35)
	b.TextColor3 = Color3.new(1,1,1)
	Instance.new("UICorner", b)
	return b
end

-- Example toggles
local NameESPBtn = MakeToggle("Name ESP")
local BoxESPBtn = MakeToggle("Box ESP")
local HighlightBtn = MakeToggle("Highlight ESP")

-- Open / Close
Toggle.MouseButton1Click:Connect(function()
	Main.Visible = not Main.Visible
end)

-- Drag (mobile safe)
local dragging, startPos, dragStart

Main.InputBegan:Connect(function(i)
	if i.UserInputType == Enum.UserInputType.Touch or i.UserInputType == Enum.UserInputType.MouseButton1 then
		dragging = true
		dragStart = i.Position
		startPos = Main.Position
	end
end)

UIS.InputChanged:Connect(function(i)
	if dragging and (i.UserInputType == Enum.UserInputType.Touch or i.UserInputType == Enum.UserInputType.MouseMovement) then
		local delta = i.Position - dragStart
		Main.Position = startPos + UDim2.fromOffset(delta.X, delta.Y)
	end
end)

UIS.InputEnded:Connect(function()
	dragging = false
end)
