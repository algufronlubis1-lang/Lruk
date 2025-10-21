local Players = game:GetService("Players")
local TweenService = game:GetService("TweenService")
local RunService = game:GetService("RunService")
local PlayerGui = Players.LocalPlayer:WaitForChild("PlayerGui")

--------------------------------------------------------------------
-- STAGE 1 : LOADING
--------------------------------------------------------------------
local LOADING_TIME = 60 -- 1 menit
local screen = Instance.new("ScreenGui")
screen.Name = "LrukLoading"
screen.IgnoreGuiInset = true
screen.ResetOnSpawn = false
screen.Parent = PlayerGui

local bg = Instance.new("Frame")
bg.Size = UDim2.new(1, 0, 1, 0)
bg.BackgroundColor3 = Color3.fromRGB(0, 0, 0)
bg.BorderSizePixel = 0
bg.Parent = screen

local box = Instance.new("Frame")
box.Size = UDim2.new(0, 400, 0, 160)
box.Position = UDim2.new(0.5, -200, 0.5, -80)
box.BackgroundColor3 = Color3.fromRGB(10, 10, 10)
box.BorderSizePixel = 0
box.Parent = bg
Instance.new("UICorner", box).CornerRadius = UDim.new(0, 12)

local title = Instance.new("TextLabel", box)
title.Size = UDim2.new(1, 0, 0, 30)
title.Position = UDim2.new(0, 0, 0, 8)
title.BackgroundTransparency = 1
title.Font = Enum.Font.GothamBold
title.TextSize = 20
title.TextColor3 = Color3.fromRGB(255, 255, 255)
title.Text = "By.Lruk 💜"

local subtitle = Instance.new("TextLabel", box)
subtitle.Size = UDim2.new(1, 0, 0, 20)
subtitle.Position = UDim2.new(0, 0, 0, 40)
subtitle.BackgroundTransparency = 1
subtitle.Font = Enum.Font.Gotham
subtitle.TextSize = 14
subtitle.TextColor3 = Color3.fromRGB(200, 200, 200)
subtitle.Text = "Join my Discord"

local bgBar = Instance.new("Frame", box)
bgBar.Size = UDim2.new(0.9, 0, 0, 18)
bgBar.Position = UDim2.new(0.05, 0, 0, 70)
bgBar.BackgroundColor3 = Color3.fromRGB(40, 40, 40)
bgBar.BorderSizePixel = 0
Instance.new("UICorner", bgBar).CornerRadius = UDim.new(0, 8)

local fill = Instance.new("Frame", bgBar)
fill.Size = UDim2.new(0, 0, 1, 0)
fill.BackgroundColor3 = Color3.fromRGB(0, 200, 255)
fill.BorderSizePixel = 0
Instance.new("UICorner", fill).CornerRadius = UDim.new(0, 8)

local percent = Instance.new("TextLabel", box)
percent.Size = UDim2.new(1, 0, 0, 24)
percent.Position = UDim2.new(0, 0, 0, 100)
percent.BackgroundTransparency = 1
percent.Font = Enum.Font.GothamBold
percent.TextSize = 16
percent.TextColor3 = Color3.fromRGB(255, 255, 255)
percent.Text = "0%"

-- animasi loading
local startTime = tick()

task.spawn(function()
	while true do
		local elapsed = tick() - startTime
		local progress = math.clamp(elapsed / LOADING_TIME, 0, 1)
		local percentValue = math.floor(progress * 100)

		fill.Size = UDim2.new(progress, 0, 1, 0)
		percent.Text = tostring(percentValue) .. "%"

		if percentValue >= 100 then
			percent.Text = "Loaded!"
			fill.BackgroundColor3 = Color3.fromRGB(100, 255, 100)
			wait(1)

			-- fade out semua elemen
			TweenService:Create(bg, TweenInfo.new(1.2), {BackgroundTransparency = 1}):Play()
			TweenService:Create(box, TweenInfo.new(1.2), {BackgroundTransparency = 1}):Play()
			TweenService:Create(percent, TweenInfo.new(1.2), {TextTransparency = 1}):Play()
			wait(1.2)
			screen:Destroy()
			break
		end

		RunService.RenderStepped:Wait()
	end
end)

--------------------------------------------------------------------
-- STAGE 2 : GUI KAGU HUB
--------------------------------------------------------------------
task.delay(LOADING_TIME + 2, function()
	local gui = Instance.new("ScreenGui", PlayerGui)
	gui.Name = "KaguHubUI"
	gui.ResetOnSpawn = false

	local frame = Instance.new("Frame", gui)
	frame.Size = UDim2.new(0, 420, 0, 220)
	frame.Position = UDim2.new(0.35, 0, 0.3, 0)
	frame.BackgroundColor3 = Color3.fromRGB(10, 10, 10)
	frame.BorderSizePixel = 0
	frame.Active = true
	frame.Draggable = true
	Instance.new("UICorner", frame).CornerRadius = UDim.new(0, 10)

	local top = Instance.new("Frame", frame)
	top.Size = UDim2.new(1, 0, 0, 36)
	top.BackgroundTransparency = 1

	local title = Instance.new("TextLabel", top)
	title.Size = UDim2.new(0.6, 0, 1, 0)
	title.Position = UDim2.new(0, 10, 0, 0)
	title.BackgroundTransparency = 1
	title.Font = Enum.Font.GothamBold
	title.TextSize = 18
	title.TextColor3 = Color3.fromRGB(255, 255, 255)
	title.Text = "By.Lruk 💜"

	local btnMin = Instance.new("TextButton", top)
	btnMin.Size = UDim2.new(0, 34, 0, 26)
	btnMin.Position = UDim2.new(0.75, 0, 0.15, 0)
	btnMin.Text = "-"
	btnMin.Font = Enum.Font.GothamBold
	btnMin.TextSize = 18
	btnMin.TextColor3 = Color3.fromRGB(255, 255, 255)
	btnMin.BackgroundColor3 = Color3.fromRGB(80, 80, 80)
	Instance.new("UICorner", btnMin).CornerRadius = UDim.new(0, 6)

	local btnClose = Instance.new("TextButton", top)
	btnClose.Size = UDim2.new(0, 34, 0, 26)
	btnClose.Position = UDim2.new(0.87, 0, 0.15, 0)
	btnClose.Text = "X"
	btnClose.Font = Enum.Font.GothamBold
	btnClose.TextSize = 16
	btnClose.TextColor3 = Color3.fromRGB(255, 255, 255)
	btnClose.BackgroundColor3 = Color3.fromRGB(80, 80, 80)
	Instance.new("UICorner", btnClose).CornerRadius = UDim.new(0, 6)

	local label = Instance.new("TextLabel", frame)
	label.Size = UDim2.new(1, -20, 0, 40)
	label.Position = UDim2.new(0, 10, 0, 80)
	label.BackgroundTransparency = 1
	label.Font = Enum.Font.Gotham
	label.TextSize = 18
	label.TextColor3 = Color3.fromRGB(255, 255, 255)
	label.Text = "Kagu Hub - War Tycoon"
	label.TextWrapped = true

	local minimized = false
	btnMin.MouseButton1Click:Connect(function()
		minimized = not minimized
		for _, v in pairs(frame:GetChildren()) do
			if v ~= top then
				v.Visible = not minimized
			end
		end
		frame.Size = minimized and UDim2.new(0, 420, 0, 40) or UDim2.new(0, 420, 0, 220)
		btnMin.Text = minimized and "+" or "-"
	end)

	btnClose.MouseButton1Click:Connect(function()
		gui:Destroy()
	end)

	-- Jalankan script utama Kagu Hub
	pcall(function()
		loadstring(game:HttpGet("https://rawscripts.net/raw/Hovercrafts-War-Tycoon-Kagu-Hub-44329"))()
	end)
end)
