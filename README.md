-- Universal.lua
-- By.Lruk

local Players = game:GetService("Players")
local LocalPlayer = Players.LocalPlayer
local PlayerGui = LocalPlayer:WaitForChild("PlayerGui")

-- Create ScreenGui (do not put in CoreGui; use PlayerGui)
local screen = Instance.new("ScreenGui")
screen.Name = "Universal_By_Lruk"
screen.ResetOnSpawn = false
screen.Parent = PlayerGui

-- Main frame
local frame = Instance.new("Frame")
frame.Size = UDim2.new(0, 240, 0, 170)
frame.Position = UDim2.new(0.38, 0, 0.32, 0)
frame.BackgroundColor3 = Color3.fromRGB(15,15,15)
frame.BorderSizePixel = 0
frame.Active = true
frame.Draggable = true
frame.Parent = screen
Instance.new("UICorner", frame).CornerRadius = UDim.new(0, 10)

-- Title
local title = Instance.new("TextLabel", frame)
title.Size = UDim2.new(0.7, 0, 0, 28)
title.Position = UDim2.new(0.06, 0, 0, 4)
title.BackgroundTransparency = 1
title.Text = "Universal"
title.TextColor3 = Color3.fromRGB(255,255,255)
title.Font = Enum.Font.GothamBold
title.TextSize = 18
title.TextXAlignment = Enum.TextXAlignment.Left

-- Minimize button
local btnMin = Instance.new("TextButton", frame)
btnMin.Size = UDim2.new(0,28,0,22)
btnMin.Position = UDim2.new(0.72, 0, 0, 4)
btnMin.Text = "-"
btnMin.Font = Enum.Font.GothamBold
btnMin.TextSize = 18
btnMin.TextColor3 = Color3.fromRGB(255,255,255)
btnMin.BackgroundColor3 = Color3.fromRGB(60,60,60)
Instance.new("UICorner", btnMin).CornerRadius = UDim.new(0,6)

-- Close button
local btnClose = Instance.new("TextButton", frame)
btnClose.Size = UDim2.new(0,28,0,22)
btnClose.Position = UDim2.new(0.85, 0, 0, 4)
btnClose.Text = "X"
btnClose.Font = Enum.Font.GothamBold
btnClose.TextSize = 16
btnClose.TextColor3 = Color3.fromRGB(255,255,255)
btnClose.BackgroundColor3 = Color3.fromRGB(60,60,60)
Instance.new("UICorner", btnClose).CornerRadius = UDim.new(0,6)

-- By.Lruk label
local byLabel = Instance.new("TextLabel", frame)
byLabel.Size = UDim2.new(0.6,0,0,18)
byLabel.Position = UDim2.new(0.06,0,0.88,0)
byLabel.Text = "By. Lruk"
byLabel.BackgroundTransparency = 1
byLabel.TextColor3 = Color3.fromRGB(255,255,255)
byLabel.Font = Enum.Font.Gotham
byLabel.TextSize = 12
byLabel.TextXAlignment = Enum.TextXAlignment.Left

-- Buttons
local function makeButton(text, ypos)
	local b = Instance.new("TextButton", frame)
	b.Size = UDim2.new(0.88,0,0,34)
	b.Position = UDim2.new(0.06,0,ypos,0)
	b.Text = text
	b.Font = Enum.Font.Gotham
	b.TextSize = 15
	b.TextColor3 = Color3.fromRGB(255,255,255)
	b.BackgroundColor3 = Color3.fromRGB(80,80,80)
	Instance.new("UICorner", b).CornerRadius = UDim.new(0,8)
	return b
end

local btnTracker = makeButton("👁️ Tracker", 0.30)
local btnSee = makeButton("👀 Tembus Pandang", 0.57)

-- Minimize / Close actions
local hidden = false
btnMin.MouseButton1Click:Connect(function()
	hidden = not hidden
	for _,v in pairs(frame:GetChildren()) do
		if v ~= title and v ~= btnMin and v ~= btnClose then
			v.Visible = not hidden
		end
	end
	frame.Size = hidden and UDim2.new(0,240,0,34) or UDim2.new(0,240,0,170)
end)

btnClose.MouseButton1Click:Connect(function()
	screen:Destroy()
end)

-- CORE FEATURES: tracker (white highlight) & see-through (local)
local RunService = game:GetService("RunService")
local highlightInst = nil
local trackerActive = false
local seeActive = false
local lastTarget = nil

local function makeWhiteHighlight(targetCharacter)
	if highlightInst then
		highlightInst:Destroy()
		highlightInst = nil
	end
	if not targetCharacter then return end
	local h = Instance.new("Highlight")
	h.FillColor = Color3.fromRGB(255,255,255)
	h.OutlineColor = Color3.fromRGB(255,255,255)
	h.FillTransparency = 0.2
	h.OutlineTransparency = 0
	h.Parent = targetCharacter
	highlightInst = h
end

local function clearHighlight()
	if highlightInst then
		pcall(function() highlightInst:Destroy() end)
		highlightInst = nil
	end
end

-- find nearest other player within radius (local player's HRP required)
local TRACK_RADIUS = 120
local function findNearest()
	local lpChar = LocalPlayer.Character
	if not lpChar or not lpChar:FindFirstChild("HumanoidRootPart") then return nil end
	local best, bd = nil, math.huge
	for _,pl in pairs(Players:GetPlayers()) do
		if pl ~= LocalPlayer and pl.Character and pl.Character:FindFirstChild("HumanoidRootPart") then
			local d = (pl.Character.HumanoidRootPart.Position - lpChar.HumanoidRootPart.Position).Magnitude
			if d < bd and d <= TRACK_RADIUS then
				bd = d
				best = pl
			end
		end
	end
	return best
end

-- Tracker loop (runs while trackerActive)
task.spawn(function()
	while true do
		task.wait(0.6)
		if trackerActive then
			local nearest = findNearest()
			if nearest and nearest.Character then
				if lastTarget ~= nearest.Character then
					clearHighlight()
					makeWhiteHighlight(nearest.Character)
					lastTarget = nearest.Character
				end
			else
				if lastTarget then
					clearHighlight()
					lastTarget = nil
				end
			end
		else
			if lastTarget then clearHighlight() lastTarget = nil end
		end
	end
end)

-- Toggle tracker
btnTracker.MouseButton1Click:Connect(function()
	trackerActive = not trackerActive
	btnTracker.Text = trackerActive and "✅ Tracker Aktif" or "👁️ Tracker"
	btnTracker.BackgroundColor3 = trackerActive and Color3.fromRGB(100,100,255) or Color3.fromRGB(80,80,80)
end)

-- Toggle see-through (local only)
btnSee.MouseButton1Click:Connect(function()
	seeActive = not seeActive
	for _,pl in pairs(Players:GetPlayers()) do
		if pl.Character then
			for _,part in pairs(pl.Character:GetDescendants()) do
				if part:IsA("BasePart") then
					-- Local transparency modifier so only you see the effect
					part.LocalTransparencyModifier = seeActive and 0.5 or 0
				end
			end
		end
	end
	btnSee.Text = seeActive and "✅ Tembus Aktif" or "👀 Tembus Pandang"
	btnSee.BackgroundColor3 = seeActive and Color3.fromRGB(100,100,255) or Color3.fromRGB(80,80,80)
end)

-- Safety: clear highlight when player respawns/exits
Players.PlayerRemoving:Connect(function(pl) if pl.Character == lastTarget then clearHighlight() lastTarget = nil end end)
Players.PlayerAdded:Connect(function(pl) pl.CharacterAdded:Connect(function() if lastTarget and lastTarget.Parent==nil then clearHighlight() lastTarget=nil end end) end)
