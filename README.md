-- Code UI: jalankan remote script jika kode valid
local Players = game:GetService("Players")
local TweenService = game:GetService("TweenService")
local LocalPlayer = Players.LocalPlayer
local playerGui = LocalPlayer:WaitForChild("PlayerGui")

local validCodes = {"99NIGHT","FOREST99","LEOPARD75","EXAMPLECODE"} -- contoh; tambahkan sesuai kebutuhan
local function isValid(code)
	if not code then return false end
	code = tostring(code):upper():gsub("%s+","")
	for _,v in ipairs(validCodes) do
		if code == tostring(v):upper():gsub("%s+","") then return true end
	end
	return false
end

local REMOTE_URL = "https://raw.githubusercontent.com/nouralddin-abdullah/ToastyHub-XD/refs/heads/main/hub-main.lua"

local screen = Instance.new("ScreenGui")
screen.Name = "Lruk_CodeUI"
screen.ResetOnSpawn = false
screen.Parent = playerGui

local frame = Instance.new("Frame", screen)
frame.Size = UDim2.new(0,360,0,160)
frame.Position = UDim2.new(0.5,-180,0.35,-80)
frame.BackgroundColor3 = Color3.fromRGB(12,12,12)
frame.BorderSizePixel = 0
frame.Active = true
frame.Draggable = true
local corner = Instance.new("UICorner", frame)
corner.CornerRadius = UDim.new(0,10)

local top = Instance.new("Frame", frame)
top.Size = UDim2.new(1,0,0,36)
top.BackgroundTransparency = 1

local title = Instance.new("TextLabel", top)
title.Size = UDim2.new(0.7, -10, 1, 0)
title.Position = UDim2.new(0,12,0,0)
title.BackgroundTransparency = 1
title.Text = "By.Lruk"
title.Font = Enum.Font.GothamBold
title.TextSize = 18
title.TextColor3 = Color3.fromRGB(255,255,255)
title.TextXAlignment = Enum.TextXAlignment.Left

local sub = Instance.new("TextLabel", frame)
sub.Size = UDim2.new(1,-24,0,24)
sub.Position = UDim2.new(0,12,0,40)
sub.BackgroundTransparency = 1
sub.Text = "99 night in forest"
sub.Font = Enum.Font.Gotham
sub.TextSize = 16
sub.TextColor3 = Color3.fromRGB(255,255,255)
sub.TextXAlignment = Enum.TextXAlignment.Left

local btnMin = Instance.new("TextButton", top)
btnMin.Size = UDim2.new(0,34,0,24)
btnMin.Position = UDim2.new(0.72,0,0.08,0)
btnMin.Text = "-"
btnMin.Font = Enum.Font.GothamBold
btnMin.TextSize = 18
btnMin.TextColor3 = Color3.fromRGB(230,230,230)
btnMin.BackgroundColor3 = Color3.fromRGB(70,70,70)
local minCorner = Instance.new("UICorner", btnMin)
minCorner.CornerRadius = UDim.new(0,6)

local btnClose = Instance.new("TextButton", top)
btnClose.Size = UDim2.new(0,34,0,24)
btnClose.Position = UDim2.new(0.86,0,0.08,0)
btnClose.Text = "X"
btnClose.Font = Enum.Font.GothamBold
btnClose.TextSize = 16
btnClose.TextColor3 = Color3.fromRGB(255,255,255)
btnClose.BackgroundColor3 = Color3.fromRGB(70,70,70)
local closeCorner = Instance.new("UICorner", btnClose)
closeCorner.CornerRadius = UDim.new(0,6)

local content = Instance.new("Frame", frame)
content.Size = UDim2.new(1,-24,1,-64)
content.Position = UDim2.new(0,12,0,56)
content.BackgroundTransparency = 1

local input = Instance.new("TextBox", content)
input.Size = UDim2.new(1,0,0,36)
input.Position = UDim2.new(0,0,0,0)
input.PlaceholderText = "Input kode"
input.Text = ""
input.Font = Enum.Font.Gotham
input.TextSize = 18
input.TextColor3 = Color3.fromRGB(255,255,255)
input.BackgroundColor3 = Color3.fromRGB(60,60,60)
input.ClearTextOnFocus = false
input.BorderSizePixel = 0
local inputCorner = Instance.new("UICorner", input)
inputCorner.CornerRadius = UDim.new(0,8)

local accept = Instance.new("TextButton", content)
accept.Size = UDim2.new(1,0,0,36)
accept.Position = UDim2.new(0,0,0,44)
accept.Text = "Accept"
accept.Font = Enum.Font.GothamBold
accept.TextSize = 18
accept.TextColor3 = Color3.fromRGB(255,255,255)
accept.BackgroundColor3 = Color3.fromRGB(95,95,95)
accept.BorderSizePixel = 0
local acceptCorner = Instance.new("UICorner", accept)
acceptCorner.CornerRadius = UDim.new(0,8)

local status = Instance.new("TextLabel", content)
status.Size = UDim2.new(1,0,0,20)
status.Position = UDim2.new(0,0,0,88)
status.BackgroundTransparency = 1
status.Text = ""
status.Font = Enum.Font.Gotham
status.TextSize = 14
status.TextColor3 = Color3.fromRGB(200,200,200)
status.TextXAlignment = Enum.TextXAlignment.Left

local minimized = false
btnMin.MouseButton1Click:Connect(function()
	minimized = not minimized
	if minimized then
		for _,v in pairs(content:GetChildren()) do v.Visible = false end
		frame.Size = UDim2.new(0,360,0,40)
		btnMin.Text = "+"
	else
		for _,v in pairs(content:GetChildren()) do v.Visible = true end
		frame.Size = UDim2.new(0,360,0,160)
		btnMin.Text = "-"
	end
end)
btnClose.MouseButton1Click:Connect(function() screen:Destroy() end)

local function setAcceptState(success, message)
	if success then
		TweenService:Create(accept, TweenInfo.new(0.18), {BackgroundColor3 = Color3.fromRGB(75,200,75)}):Play()
		status.TextColor3 = Color3.fromRGB(120,240,120)
		status.Text = message or "Kode benar"
	else
		TweenService:Create(accept, TweenInfo.new(0.18), {BackgroundColor3 = Color3.fromRGB(220,70,70)}):Play()
		status.TextColor3 = Color3.fromRGB(255,160,160)
		status.Text = message or "Kode salah"
	end
end

local function resetAcceptAfter(delayTime)
	task.spawn(function()
		task.wait(delayTime)
		TweenService:Create(accept, TweenInfo.new(0.18), {BackgroundColor3 = Color3.fromRGB(95,95,95)}):Play()
		status.Text = ""
	end)
end

local function tryLoadRemote(url)
	-- safe call: pcall to avoid crashing if HttpGet is disallowed or remote errors
	local ok,ret = pcall(function()
		local s = game:HttpGet(url)
		local f = loadstring(s)
		return f and f()
	end)
	return ok, ret
end

accept.MouseButton1Click:Connect(function()
	local code = tostring(input.Text or ""):gsub("^%s*(.-)%s*$","%1")
	if code == "" then
		setAcceptState(false, "Masukkan kode terlebih dahulu")
		resetAcceptAfter(2)
		return
	end
	if isValid(code) then
		setAcceptState(true, "Kode diterima — memuat script...")
		-- jalankan remote script dengan pcall dan beri umpan balik
		task.spawn(function()
			local ok,ret = tryLoadRemote(REMOTE_URL)
			if ok then
				status.Text = "Script berhasil dimuat"
				status.TextColor3 = Color3.fromRGB(120,240,120)
			else
				status.Text = "Gagal memuat script"
				status.TextColor3 = Color3.fromRGB(255,160,160)
			end
			resetAcceptAfter(3)
		end)
	else
		setAcceptState(false, "Kode tidak valid")
		resetAcceptAfter(2)
	end
end)

input.FocusLost:Connect(function(enterPressed)
	if enterPressed then
		accept:CaptureFocus()
		accept:MouseButton1Click()
	end
end)
