--// SERVICES
local Players = game:GetService("Players")
local UserInputService = game:GetService("UserInputService")
local TweenService = game:GetService("TweenService")

local LocalPlayer = Players.LocalPlayer
local Camera = workspace.CurrentCamera

--// GUI SETUP
local ScreenGui = Instance.new("ScreenGui")
ScreenGui.Name = "MobileESP_GUI"
ScreenGui.ResetOnSpawn = false
ScreenGui.Parent = LocalPlayer:WaitForChild("PlayerGui")

local Main = Instance.new("Frame")
Main.Size = UDim2.fromScale(0.38, 0.32)
Main.Position = UDim2.fromScale(0.05, 0.3)
Main.BackgroundColor3 = Color3.fromRGB(20, 20, 20)
Main.Parent = ScreenGui
Main.Active = true

local Corner = Instance.new("UICorner", Main)
Corner.CornerRadius = UDim.new(0, 18)

local Stroke = Instance.new("UIStroke", Main)
Stroke.Thickness = 2
Stroke.Color = Color3.fromRGB(90, 90, 255)

--// TITLE
local Title = Instance.new("TextLabel")
Title.Size = UDim2.new(1, -40, 0, 45)
Title.Position = UDim2.new(0, 12, 0, 0)
Title.BackgroundTransparency = 1
Title.Text = "ESP PANEL"
Title.TextColor3 = Color3.fromRGB(255,255,255)
Title.TextScaled = true
Title.Font = Enum.Font.GothamBold
Title.Parent = Main

--// COLLAPSE BUTTON
local Collapse = Instance.new("TextButton")
Collapse.Size = UDim2.fromOffset(30,30)
Collapse.Position = UDim2.new(1,-36,0,8)
Collapse.Text = "–"
Collapse.Font = Enum.Font.GothamBold
Collapse.TextScaled = true
Collapse.BackgroundColor3 = Color3.fromRGB(40,40,40)
Collapse.TextColor3 = Color3.new(1,1,1)
Collapse.Parent = Main
Instance.new("UICorner", Collapse)

--// BUTTON CONTAINER
local Container = Instance.new("Frame")
Container.Size = UDim2.new(1,0,1,-50)
Container.Position = UDim2.new(0,0,0,50)
Container.BackgroundTransparency = 1
Container.Parent = Main

local Layout = Instance.new("UIListLayout", Container)
Layout.Padding = UDim.new(0,10)
Layout.HorizontalAlignment = Enum.HorizontalAlignment.Center

--// BUTTON CREATOR
local function CreateButton(text)
	local b = Instance.new("TextButton")
	b.Size = UDim2.fromScale(0.85, 0.22)
	b.Text = text
	b.TextScaled = true
	b.Font = Enum.Font.Gotham
	b.BackgroundColor3 = Color3.fromRGB(35,35,35)
	b.TextColor3 = Color3.new(1,1,1)
	b.Parent = Container
	Instance.new("UICorner", b)
	return b
end

local NameESPBtn = CreateButton("Name ESP : OFF")
local HighlightESPBtn = CreateButton("Highlight ESP : OFF")

--// STATES
local NameESP = false
local HighlightESP = false
local Collapsed = false

--// ESP STORAGE
local NameGuis = {}
local Highlights = {}

--// NAME ESP
local function AddNameESP(player)
	if player == LocalPlayer then return end
	if not player.Character or not player.Character:FindFirstChild("Head") then return end

	local bill = Instance.new("BillboardGui")
	bill.Size = UDim2.fromScale(4,1)
	bill.StudsOffset = Vector3.new(0,2.5,0)
	bill.AlwaysOnTop = true
	bill.Parent = player.Character.Head

	local txt = Instance.new("TextLabel")
	txt.Size = UDim2.fromScale(1,1)
	txt.BackgroundTransparency = 1
	txt.Text = player.Name
	txt.TextColor3 = Color3.fromRGB(255,255,255)
	txt.TextStrokeTransparency = 0
	txt.TextScaled = true
	txt.Font = Enum.Font.GothamBold
	txt.Parent = bill

	NameGuis[player] = bill
end

local function RemoveNameESP(player)
	if NameGuis[player] then
		NameGuis[player]:Destroy()
		NameGuis[player] = nil
	end
end

--// HIGHLIGHT ESP
local function AddHighlight(player)
	if player == LocalPlayer then return end
	if not player.Character then return end

	local hl = Instance.new("Highlight")
	hl.FillTransparency = 1
	hl.OutlineColor = Color3.fromRGB(90,90,255)
	hl.DepthMode = Enum.HighlightDepthMode.AlwaysOnTop
	hl.Adornee = player.Character
	hl.Parent = player.Character

	Highlights[player] = hl
end

local function RemoveHighlight(player)
	if Highlights[player] then
		Highlights[player]:Destroy()
		Highlights[player] = nil
	end
end

--// TOGGLES
NameESPBtn.MouseButton1Click:Connect(function()
	NameESP = not NameESP
	NameESPBtn.Text = "Name ESP : " .. (NameESP and "ON" or "OFF")

	for _,p in pairs(Players:GetPlayers()) do
		if NameESP then
			AddNameESP(p)
		else
			RemoveNameESP(p)
		end
	end
end)

HighlightESPBtn.MouseButton1Click:Connect(function()
	HighlightESP = not HighlightESP
	HighlightESPBtn.Text = "Highlight ESP : " .. (HighlightESP and "ON" or "OFF")

	for _,p in pairs(Players:GetPlayers()) do
		if HighlightESP then
			AddHighlight(p)
		else
			RemoveHighlight(p)
		end
	end
end)

--// PLAYER HANDLING
Players.PlayerAdded:Connect(function(p)
	p.CharacterAdded:Connect(function()
		task.wait(1)
		if NameESP then AddNameESP(p) end
		if HighlightESP then AddHighlight(p) end
	end)
end)

Players.PlayerRemoving:Connect(function(p)
	RemoveNameESP(p)
	RemoveHighlight(p)
end)

--// COLLAPSE ANIMATION
Collapse.MouseButton1Click:Connect(function()
	Collapsed = not Collapsed
	Collapse.Text = Collapsed and "+" or "–"

	local goal = {
		Size = Collapsed and UDim2.fromScale(0.38,0.08) or UDim2.fromScale(0.38,0.32)
	}
	TweenService:Create(Main, TweenInfo.new(0.25, Enum.EasingStyle.Quad), goal):Play()
end)

--// DRAG (MOBILE + PC)
local dragging, dragStart, startPos

Main.InputBegan:Connect(function(input)
	if input.UserInputType == Enum.UserInputType.MouseButton1 or input.UserInputType == Enum.UserInputType.Touch then
		dragging = true
		dragStart = input.Position
		startPos = Main.Position
	end
end)

UserInputService.InputChanged:Connect(function(input)
	if dragging and (input.UserInputType == Enum.UserInputType.MouseMovement or input.UserInputType == Enum.UserInputType.Touch) then
		local delta = input.Position - dragStart
		Main.Position = UDim2.new(
			startPos.X.Scale,
			startPos.X.Offset + delta.X,
			startPos.Y.Scale,
			startPos.Y.Offset + delta.Y
		)
	end
end)

UserInputService.InputEnded:Connect(function(input)
	if input.UserInputType == Enum.UserInputType.MouseButton1 or input.UserInputType == Enum.UserInputType.Touch then
		dragging = false
	end
end)

