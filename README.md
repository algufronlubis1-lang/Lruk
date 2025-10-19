-- Universal UI By.Lruk (LocalScript)
-- UI hitam halus, teks putih, tombol abu-abu
local Players = game:GetService("Players")
local RunService = game:GetService("RunService")
local LocalPlayer = Players.LocalPlayer
local PlayerGui = LocalPlayer:WaitForChild("PlayerGui")
local Workspace = workspace

-- radii
local TRACK_RADIUS = 150
local ESP_RADIUS = 200
local NAME_RADIUS = 200

-- state
local trackerOn, espOn, nameOn, invisibleOn = false, false, false, false
local beams, boxes, nameGuis = {}, {}, {}
local localTransparencyRecords = {} -- store original LocalTransparencyModifier per part

-- safe destroy helper
local function safeDestroy(o) if o and o.Parent then pcall(function() o:Destroy() end) end end
local function getHRP(pl) return pl.Character and pl.Character:FindFirstChild("HumanoidRootPart") end

-- build UI
local screen = Instance.new("ScreenGui"); screen.Name = "UniversalLruk_UI"; screen.ResetOnSpawn = false; screen.Parent = PlayerGui
local frame = Instance.new("Frame", screen); frame.Size = UDim2.new(0,420,0,320); frame.Position = UDim2.new(0.35,0,0.22,0); frame.BackgroundColor3 = Color3.fromRGB(12,12,12); frame.Active = true; frame.Draggable = true; frame.BorderSizePixel = 0; Instance.new("UICorner", frame).CornerRadius = UDim.new(0,12)
local top = Instance.new("Frame", frame); top.Size = UDim2.new(1,0,0,44); top.BackgroundTransparency = 1
local title = Instance.new("TextLabel", top); title.Size = UDim2.new(0.6,-10,1,0); title.Position = UDim2.new(0,12,0,0); title.BackgroundTransparency = 1; title.Text = "By.Lruk 💜"; title.Font = Enum.Font.GothamBold; title.TextSize = 18; title.TextColor3 = Color3.fromRGB(255,255,255); title.TextXAlignment = Enum.TextXAlignment.Left
local btnMin = Instance.new("TextButton", top); btnMin.Size = UDim2.new(0,36,0,30); btnMin.Position = UDim2.new(0.72,0,0.06,0); btnMin.Text="-"; btnMin.Font=Enum.Font.GothamBold; btnMin.TextSize=18; btnMin.TextColor3=Color3.fromRGB(255,255,255); btnMin.BackgroundColor3=Color3.fromRGB(80,80,80); Instance.new("UICorner", btnMin).CornerRadius=UDim.new(0,6)
local btnClose = Instance.new("TextButton", top); btnClose.Size = UDim2.new(0,36,0,30); btnClose.Position = UDim2.new(0.86,0,0.06,0); btnClose.Text="X"; btnClose.Font=Enum.Font.GothamBold; btnClose.TextSize=16; btnClose.TextColor3=Color3.fromRGB(255,255,255); btnClose.BackgroundColor3=Color3.fromRGB(80,80,80); Instance.new("UICorner", btnClose).CornerRadius=UDim.new(0,6)
local content = Instance.new("Frame", frame); content.Size = UDim2.new(1,-20,1,-64); content.Position = UDim2.new(0,10,0,54); content.BackgroundTransparency = 1

-- left column
local left = Instance.new("Frame", content); left.Size = UDim2.new(0.47,0,1,0); left.Position = UDim2.new(0,0,0,0); left.BackgroundTransparency = 1
local function makeButton(parent, text, y) local b = Instance.new("TextButton", parent); b.Size = UDim2.new(0.9,0,0,36); b.Position = UDim2.new(0.05,0,y,0); b.Text = text; b.Font = Enum.Font.Gotham; b.TextSize = 15; b.TextColor3 = Color3.fromRGB(255,255,255); b.BackgroundColor3 = Color3.fromRGB(90,90,90); Instance.new("UICorner", b).CornerRadius = UDim.new(0,8); return b end
local btnKill = makeButton(left, "Kill Aura (Void)", 6)
local btnInvisible = makeButton(left, "Invisible (client)", 52)
local btnTracker = makeButton(left, "Tracker", 98)
local btnESP = makeButton(left, "ESP (Teambox)", 144)
local btnName = makeButton(left, "Name Tags", 190)
local statusLabel = Instance.new("TextLabel", left); statusLabel.Size = UDim2.new(0.9,0,0,22); statusLabel.Position = UDim2.new(0.05,0,0,236); statusLabel.BackgroundTransparency = 1; statusLabel.Text = "Status: idle"; statusLabel.Font = Enum.Font.Gotham; statusLabel.TextSize = 14; statusLabel.TextColor3 = Color3.fromRGB(200,200,200); statusLabel.TextXAlignment = Enum.TextXAlignment.Left

-- right column (player list, TP)
local right = Instance.new("Frame", content); right.Size = UDim2.new(0.5,0,1,0); right.Position = UDim2.new(0.49,0,0,0); right.BackgroundTransparency = 1
local playersTitle = Instance.new("TextLabel", right); playersTitle.Size = UDim2.new(1,0,0,24); playersTitle.Position = UDim2.new(0,0,0,0); playersTitle.BackgroundTransparency = 1; playersTitle.Text = "Players (click to select)"; playersTitle.Font = Enum.Font.Gotham; playersTitle.TextSize = 14; playersTitle.TextColor3 = Color3.fromRGB(255,255,255); playersTitle.TextXAlignment = Enum.TextXAlignment.Left
local list = Instance.new("ScrollingFrame", right); list.Size = UDim2.new(1,0,0,220); list.Position = UDim2.new(0,0,0,30); list.BackgroundTransparency = 1; list.ScrollBarThickness = 6; local listLayout = Instance.new("UIListLayout", list); listLayout.Padding = UDim.new(0,6)
local selectedLabel = Instance.new("TextLabel", right); selectedLabel.Size = UDim2.new(1,0,0,24); selectedLabel.Position = UDim2.new(0,0,0,258); selectedLabel.BackgroundTransparency = 1; selectedLabel.Text = "Selected: (none)"; selectedLabel.Font = Enum.Font.Gotham; selectedLabel.TextSize = 14; selectedLabel.TextColor3 = Color3.fromRGB(200,200,200); selectedLabel.TextXAlignment = Enum.TextXAlignment.Left
local tpButton = Instance.new("TextButton", right); tpButton.Size = UDim2.new(0.9,0,0,36); tpButton.Position = UDim2.new(0.05,0,0,290); tpButton.Text = "Teleport to Selected"; tpButton.Font = Enum.Font.Gotham; tpButton.TextSize = 15; tpButton.TextColor3 = Color3.fromRGB(255,255,255); tpButton.BackgroundColor3 = Color3.fromRGB(90,90,90); Instance.new("UICorner", tpButton).CornerRadius = UDim.new(0,8)
local inputBox = Instance.new("TextBox", right); inputBox.Size = UDim2.new(0.9,0,0,28); inputBox.Position = UDim2.new(0.05,0,0,336); inputBox.PlaceholderText = "(optional) type name"; inputBox.Font = Enum.Font.Gotham; inputBox.TextSize = 14; inputBox.TextColor3 = Color3.fromRGB(255,255,255); inputBox.BackgroundColor3 = Color3.fromRGB(60,60,60); inputBox.ClearTextOnFocus = false; Instance.new("UICorner", inputBox).CornerRadius = UDim.new(0,6)

local selectedPlayer = nil
local function refreshPlayerList()
	for _,c in pairs(list:GetChildren()) do if not c:IsA("UIListLayout") then safeDestroy(c) end end
	local y = 6
	for _,pl in pairs(Players:GetPlayers()) do
		if pl ~= LocalPlayer then
			local b = Instance.new("TextButton", list)
			b.Size = UDim2.new(1,-12,0,28)
			b.Position = UDim2.new(0,6,0,y)
			b.BackgroundColor3 = Color3.fromRGB(80,80,80)
			b.TextColor3 = Color3.fromRGB(255,255,255)
			b.Font = Enum.Font.Gotham
			b.TextSize = 14
			b.Text = pl.Name
			Instance.new("UICorner", b).CornerRadius = UDim.new(0,6)
			b.MouseButton1Click:Connect(function() selectedPlayer = pl; selectedLabel.Text = "Selected: "..pl.Name end)
			y = y + 34
		end
	end
	list.CanvasSize = UDim2.new(0,0,0,math.max(0,y))
end

-- create/remove visuals
local function createBeam(pl)
	if beams[pl] then return end
	local myHRP = getHRP(LocalPlayer); local hrp = getHRP(pl)
	if not myHRP or not hrp then return end
	local a0 = Instance.new("Attachment", myHRP); local a1 = Instance.new("Attachment", hrp)
	local beam = Instance.new("Beam"); beam.Attachment0 = a0; beam.Attachment1 = a1; beam.Width0 = 0.12; beam.Width1 = 0.12; beam.Transparency = NumberSequence.new(0); beam.Color = ColorSequence.new(Color3.fromRGB(255,255,255)); beam.FaceCamera = true; beam.Parent = Workspace
	beams[pl] = {beam=beam,a0=a0,a1=a1}
end
local function removeBeam(pl) if beams[pl] then safeDestroy(beams[pl].beam); safeDestroy(beams[pl].a0); safeDestroy(beams[pl].a1); beams[pl]=nil end end
local function createBox(pl) if boxes[pl] or not pl.Character then return end local box=Instance.new("BoxHandleAdornment"); box.Adornee=pl.Character; box.Size=Vector3.new(4,6,1); box.Color3=Color3.fromRGB(255,0,0); box.Transparency=0.25; box.AlwaysOnTop=true; box.ZIndex=2; box.Parent=Workspace; boxes[pl]=box end
local function removeBox(pl) if boxes[pl] then safeDestroy(boxes[pl]); boxes[pl]=nil end end
local function createName(pl) if nameGuis[pl] or not getHRP(pl) then return end local bg=Instance.new("BillboardGui"); bg.Size=UDim2.new(0,150,0,30); bg.Adornee=getHRP(pl); bg.StudsOffset=Vector3.new(0,2.8,0); bg.AlwaysOnTop=true; bg.Parent=Workspace; local label=Instance.new("TextLabel",bg); label.Size=UDim2.new(1,0,1,0); label.BackgroundTransparency=1; label.Text=pl.Name; label.TextColor3=Color3.fromRGB(255,255,255); label.Font=Enum.Font.Gotham; label.TextSize=14; nameGuis[pl]=bg end
local function removeName(pl) if nameGuis[pl] then safeDestroy(nameGuis[pl]); nameGuis[pl]=nil end end

-- invisible (client-side only)
local function enableClientInvisible()
	local ch = LocalPlayer.Character
	if not ch then return end
	for _,part in pairs(ch:GetDescendants()) do
		if part:IsA("BasePart") then
			-- store original LocalTransparencyModifier (default 0)
			localTransparencyRecords[part] = part.LocalTransparencyModifier or 0
			pcall(function() part.LocalTransparencyModifier = 1 end)
		elseif part:IsA("Decal") or part:IsA("Texture") then
			-- hide face decals locally
			pcall(function() part.Transparency = 1 end)
		end
	end
	-- hide accessories' bodyparts (welded parts)
	for _,acc in pairs(ch:GetChildren()) do
		if acc:IsA("Accessory") and acc.Handle then
			localTransparencyRecords[acc.Handle] = acc.Handle.LocalTransparencyModifier or 0
			pcall(function() acc.Handle.LocalTransparencyModifier = 1 end)
		end
	end
end
local function disableClientInvisible()
	local ch = LocalPlayer.Character
	for part,orig in pairs(localTransparencyRecords) do
		if part and part.Parent then
			pcall(function() part.LocalTransparencyModifier = orig end)
		end
	end
	localTransparencyRecords = {}
	-- can't reliably restore decal transparency changed earlier (they are global), so we avoid changing decal.Transparency in enableClientInvisible (we left only LocalTransparencyModifier)
end

-- kill self
local function killSelfVoid()
	local ch = LocalPlayer.Character
	if not ch then return end
	local hrp = ch:FindFirstChild("HumanoidRootPart")
	local hum = ch:FindFirstChildWhichIsA("Humanoid")
	if hrp then hrp.CFrame = hrp.CFrame * CFrame.new(0, -10000, 0) end
	task.wait(1)
	if hum and hum.Health > 0 then hum.Health = 0 end
end

-- teleport to selected
local function teleportToSelected()
	local targetName = inputBox.Text:match("%S+")
	local target = nil
	if targetName and targetName ~= "" then target = Players:FindFirstChild(targetName) elseif selectedPlayer then target = selectedPlayer end
	if target and getHRP(target) and getHRP(LocalPlayer) then pcall(function() getHRP(LocalPlayer).CFrame = getHRP(target).CFrame + Vector3.new(0,4,0) end) end
end

-- main loop for visuals
RunService.RenderStepped:Connect(function()
	local myHRP = getHRP(LocalPlayer)
	if not myHRP then for p,_ in pairs(beams) do removeBeam(p) end for p,_ in pairs(boxes) do removeBox(p) end for p,_ in pairs(nameGuis) do removeName(p) end return end
	for _,pl in pairs(Players:GetPlayers()) do
		if pl ~= LocalPlayer and pl.Character and getHRP(pl) then
			local dist = (getHRP(pl).Position - myHRP.Position).Magnitude
			if trackerOn and dist <= TRACK_RADIUS then createBeam(pl) else removeBeam(pl) end
			if espOn and dist <= ESP_RADIUS then createBox(pl) else removeBox(pl) end
			if nameOn and dist <= NAME_RADIUS then createName(pl) else removeName(pl) end
		else
			removeBeam(pl); removeBox(pl); removeName(pl)
		end
	end
end)

-- button events
btnKill.MouseButton1Click:Connect(function()
	btnKill.BackgroundColor3 = Color3.fromRGB(200,80,80)
	statusLabel.Text = "Status: KillAura triggered"
	killSelfVoid()
	task.wait(0.5)
	btnKill.BackgroundColor3 = Color3.fromRGB(90,90,90)
	statusLabel.Text = "Status: idle"
end)
btnInvisible.MouseButton1Click:Connect(function()
	invisibleOn = not invisibleOn
	if invisibleOn then
		btnInvisible.BackgroundColor3 = Color3.fromRGB(100,200,255)
		statusLabel.Text = "Status: Invisible (client) ON"
		enableClientInvisible()
	else
		btnInvisible.BackgroundColor3 = Color3.fromRGB(90,90,90)
		statusLabel.Text = "Status: Invisible OFF"
		disableClientInvisible()
	end
end)
btnTracker.MouseButton1Click:Connect(function()
	trackerOn = not trackerOn
	btnTracker.BackgroundColor3 = trackerOn and Color3.fromRGB(100,100,255) or Color3.fromRGB(90,90,90)
	statusLabel.Text = "Status: Tracker " .. (trackerOn and "ON" or "OFF")
end)
btnESP.MouseButton1Click:Connect(function()
	espOn = not espOn
	btnESP.BackgroundColor3 = espOn and Color3.fromRGB(255,80,80) or Color3.fromRGB(90,90,90)
	statusLabel.Text = "Status: ESP " .. (espOn and "ON" or "OFF")
end)
btnName.MouseButton1Click:Connect(function()
	nameOn = not nameOn
	btnName.BackgroundColor3 = nameOn and Color3.fromRGB(100,100,255) or Color3.fromRGB(90,90,90)
	statusLabel.Text = "Status: Name " .. (nameOn and "ON" or "OFF")
end)

tpButton.MouseButton1Click:Connect(function() teleportToSelected() end)
Players.PlayerAdded:Connect(function() refreshPlayerList() end)
Players.PlayerRemoving:Connect(function(pl) refreshPlayerList(); removeBeam(pl); removeBox(pl); removeName(pl); if selectedPlayer==pl then selectedPlayer=nil; selectedLabel.Text="Selected: (none)" end end)

-- minimize & close
local minimized = false
btnMin.MouseButton1Click:Connect(function()
	minimized = not minimized
	content.Visible = not minimized
	frame.Size = minimized and UDim2.new(0,420,0,44) or UDim2.new(0,420,0,320)
	btnMin.Text = minimized and "+" or "-"
end)
btnClose.MouseButton1Click:Connect(function()
	for p,_ in pairs(beams) do removeBeam(p) end
	for p,_ in pairs(boxes) do removeBox(p) end
	for p,_ in pairs(nameGuis) do removeName(p) end
	disableClientInvisible()
	safeDestroy(screen)
end)

-- init
refreshPlayerList()
