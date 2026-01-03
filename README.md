--// SERVICES
local Players = game:GetService("Players")
local UIS = game:GetService("UserInputService")
local TweenService = game:GetService("TweenService")
local Lighting = game:GetService("Lighting")

local LP = Players.LocalPlayer

--// BLUR
local blur = Instance.new("BlurEffect")
blur.Size = 0
blur.Parent = Lighting

--// KEY GUI
local KeyGui = Instance.new("ScreenGui", LP.PlayerGui)
KeyGui.Name = "HumMagKey"
KeyGui.ResetOnSpawn = false

local KeyFrame = Instance.new("Frame", KeyGui)
KeyFrame.Size = UDim2.fromScale(0.45,0.25)
KeyFrame.Position = UDim2.fromScale(0.275,0.35)
KeyFrame.BackgroundColor3 = Color3.fromRGB(18,18,18)
KeyFrame.Active = true
Instance.new("UICorner", KeyFrame).CornerRadius = UDim.new(0,20)
Instance.new("UIStroke", KeyFrame).Color = Color3.fromRGB(120,120,255)

local Title = Instance.new("TextLabel", KeyFrame)
Title.Size = UDim2.new(1,0,0,45)
Title.BackgroundTransparency = 1
Title.Text = "HumMag Scripts"
Title.Font = Enum.Font.GothamBold
Title.TextScaled = true
Title.TextColor3 = Color3.new(1,1,1)

local KeyBox = Instance.new("TextBox", KeyFrame)
KeyBox.Size = UDim2.fromScale(0.85,0.28)
KeyBox.Position = UDim2.fromScale(0.075,0.4)
KeyBox.PlaceholderText = "Enter Key"
KeyBox.Font = Enum.Font.Gotham
KeyBox.TextScaled = true
KeyBox.BackgroundColor3 = Color3.fromRGB(30,30,30)
KeyBox.TextColor3 = Color3.new(1,1,1)
Instance.new("UICorner", KeyBox)

local Submit = Instance.new("TextButton", KeyFrame)
Submit.Size = UDim2.fromScale(0.6,0.22)
Submit.Position = UDim2.fromScale(0.2,0.72)
Submit.Text = "UNLOCK"
Submit.Font = Enum.Font.GothamBold
Submit.TextScaled = true
Submit.BackgroundColor3 = Color3.fromRGB(60,60,120)
Submit.TextColor3 = Color3.new(1,1,1)
Instance.new("UICorner", Submit)

local Status = Instance.new("TextLabel", KeyFrame)
Status.Size = UDim2.new(1,0,0,28)
Status.Position = UDim2.new(0,0,1,-28)
Status.BackgroundTransparency = 1
Status.Text = ""
Status.Font = Enum.Font.Gotham
Status.TextScaled = true

TweenService:Create(blur, TweenInfo.new(0.4), {Size = 20}):Play()

--// DRAG (KEY GUI)
local dragging, startPos, dragStart
KeyFrame.InputBegan:Connect(function(i)
	if i.UserInputType == Enum.UserInputType.Touch or i.UserInputType == Enum.UserInputType.MouseButton1 then
		dragging = true
		dragStart = i.Position
		startPos = KeyFrame.Position
	end
end)

UIS.InputChanged:Connect(function(i)
	if dragging and (i.UserInputType == Enum.UserInputType.Touch or i.UserInputType == Enum.UserInputType.MouseMovement) then
		local delta = i.Position - dragStart
		KeyFrame.Position = startPos + UDim2.fromOffset(delta.X, delta.Y)
	end
end)

UIS.InputEnded:Connect(function()
	dragging = false
end)

--// ESP STORAGE
local NameESP = false
local HighlightESP = false
local NameGuis = {}
local Highlights = {}

local function clearESP(p)
	if NameGuis[p] then NameGuis[p]:Destroy() NameGuis[p] = nil end
	if Highlights[p] then Highlights[p]:Destroy() Highlights[p] = nil end
end

local function applyESP(p)
	if p == LP or not p.Character then return end
	if NameESP and p.Character:FindFirstChild("Head") then
		local bb = Instance.new("BillboardGui", p.Character.Head)
		bb.Size = UDim2.fromScale(4,1)
		bb.StudsOffset = Vector3.new(0,2.5,0)
		bb.AlwaysOnTop = true

		local txt = Instance.new("TextLabel", bb)
		txt.Size = UDim2.fromScale(1,1)
		txt.BackgroundTransparency = 1
		txt.Text = p.Name
		txt.TextScaled = true
		txt.TextStrokeTransparency = 0
		txt.Font = Enum.Font.GothamBold
		txt.TextColor3 = Color3.new(1,1,1)

		NameGuis[p] = bb
	end

	if HighlightESP then
		local hl = Instance.new("Highlight", p.Character)
		hl.FillTransparency = 1
		hl.OutlineColor = Color3.fromRGB(120,120,255)
		hl.DepthMode = Enum.HighlightDepthMode.AlwaysOnTop
		Highlights[p] = hl
	end
end

--// MAIN GUI FUNCTION
local function createMainGui()
	local Gui = Instance.new("ScreenGui", LP.PlayerGui)
	Gui.Name = "HumMagMain"
	Gui.ResetOnSpawn = false

	local Panel = Instance.new("Frame", Gui)
	Panel.Size = UDim2.fromScale(0.4,0.32)
	Panel.Position = UDim2.fromScale(0.3,0.35)
	Panel.BackgroundColor3 = Color3.fromRGB(18,18,18)
	Panel.Active = true
	Instance.new("UICorner", Panel).CornerRadius = UDim.new(0,22)
	Instance.new("UIStroke", Panel).Color = Color3.fromRGB(120,120,255)

	local T = Instance.new("TextLabel", Panel)
	T.Size = UDim2.new(1,0,0,45)
	T.BackgroundTransparency = 1
	T.Text = "HumMag ESP"
	T.Font = Enum.Font.GothamBold
	T.TextScaled = true
	T.TextColor3 = Color3.new(1,1,1)

	local Collapse = Instance.new("TextButton", Panel)
	Collapse.Size = UDim2.fromOffset(30,30)
	Collapse.Position = UDim2.new(1,-38,0,8)
	Collapse.Text = "–"
	Collapse.Font = Enum.Font.GothamBold
	Collapse.TextScaled = true
	Collapse.BackgroundColor3 = Color3.fromRGB(30,30,30)
	Collapse.TextColor3 = Color3.new(1,1,1)
	Instance.new("UICorner", Collapse)

	local Content = Instance.new("Frame", Panel)
	Content.Size = UDim2.new(1,0,1,-50)
	Content.Position = UDim2.new(0,0,0,50)
	Content.BackgroundTransparency = 1

	local Layout = Instance.new("UIListLayout", Content)
	Layout.Padding = UDim.new(0,10)
	Layout.HorizontalAlignment = Enum.HorizontalAlignment.Center

	local function makeButton(text)
		local b = Instance.new("TextButton", Content)
		b.Size = UDim2.fromScale(0.85,0.18)
		b.Text = text .. ": OFF"
		b.Font = Enum.Font.Gotham
		b.TextScaled = true
		b.BackgroundColor3 = Color3.fromRGB(35,35,35)
		b.TextColor3 = Color3.new(1,1,1)
		Instance.new("UICorner", b)
		return b
	end

	local NameBtn = makeButton("Name ESP")
	local HLBtn = makeButton("Highlight ESP")

	NameBtn.MouseButton1Click:Connect(function()
		NameESP = not NameESP
		NameBtn.Text = "Name ESP: " .. (NameESP and "ON" or "OFF")
		for _,p in ipairs(Players:GetPlayers()) do
			clearESP(p)
			applyESP(p)
		end
	end)

	HLBtn.MouseButton1Click:Connect(function()
		HighlightESP = not HighlightESP
		HLBtn.Text = "Highlight ESP: " .. (HighlightESP and "ON" or "OFF")
		for _,p in ipairs(Players:GetPlayers()) do
			clearESP(p)
			applyESP(p)
		end
	end)

	local collapsed = false
	Collapse.MouseButton1Click:Connect(function()
		collapsed = not collapsed
		Content.Visible = not collapsed
		Collapse.Text = collapsed and "+" or "–"
	end)

	-- DRAG MAIN GUI
	local d, ds, sp
	Panel.InputBegan:Connect(function(i)
		if i.UserInputType == Enum.UserInputType.Touch or i.UserInputType == Enum.UserInputType.MouseButton1 then
			d = true ds = i.Position sp = Panel.Position
		end
	end)

	UIS.InputChanged:Connect(function(i)
		if d and (i.UserInputType == Enum.UserInputType.Touch or i.UserInputType == Enum.UserInputType.MouseMovement) then
			local delta = i.Position - ds
			Panel.Position = sp + UDim2.fromOffset(delta.X, delta.Y)
		end
	end)

	UIS.InputEnded:Connect(function() d = false end)

	Players.PlayerAdded:Connect(function(p)
		p.CharacterAdded:Connect(function()
			task.wait(1)
			applyESP(p)
		end)
	end)
end

--// KEY CHECK
local KEY = "HumMagScripts1"

Submit.MouseButton1Click:Connect(function()
	if KeyBox.Text == KEY then
		TweenService:Create(blur, TweenInfo.new(0.4), {Size = 0}):Play()
		KeyGui:Destroy()
		createMainGui()
	else
		Status.Text = "Invalid Key"
		Status.TextColor3 = Color3.fromRGB(255,80,80)
	end
end)
