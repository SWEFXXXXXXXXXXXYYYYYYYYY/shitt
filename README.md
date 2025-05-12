local player = game.Players.LocalPlayer
local gui = Instance.new("ScreenGui", player:WaitForChild("PlayerGui"))
gui.Name = "JunusDenusMenu"
gui.ResetOnSpawn = false

-- Menu Button
local menuButton = Instance.new("TextButton")
menuButton.Size = UDim2.new(0, 100, 0, 30)
menuButton.Position = UDim2.new(0, 10, 0, 10)
menuButton.Text = "junus denus"
menuButton.BackgroundColor3 = Color3.fromRGB(70, 70, 70)
menuButton.TextColor3 = Color3.new(1, 1, 1)
menuButton.Parent = gui

-- Sound for opening menu
local sound = Instance.new("Sound", gui)
sound.SoundId = "rbxassetid://12221967"  -- You can change the ID here for different sound effects
sound.Volume = 1

-- Panel
local panel = Instance.new("Frame")
panel.Size = UDim2.new(0, 240, 0, 350)
panel.Position = UDim2.new(0.5, -120, 0.5, -175)
panel.BackgroundColor3 = Color3.fromRGB(40, 40, 40)
panel.Visible = false
panel.Active = true
panel.Draggable = true
panel.Parent = gui

-- Close Button (X)
local closeButton = Instance.new("TextButton")
closeButton.Size = UDim2.new(0, 25, 0, 25)
closeButton.Position = UDim2.new(1, -30, 0, 5)
closeButton.Text = "X"
closeButton.BackgroundColor3 = Color3.fromRGB(128, 0, 128)  -- Purple
closeButton.TextColor3 = Color3.new(1, 1, 1)
closeButton.Parent = panel

closeButton.MouseButton1Click:Connect(function()
	panel.Visible = false
end)

-- Button creator
local function createButton(name, y)
	local btn = Instance.new("TextButton")
	btn.Size = UDim2.new(0.8, 0, 0, 30)
	btn.Position = UDim2.new(0.1, 0, 0, y)
	btn.Text = name
	btn.BackgroundColor3 = Color3.fromRGB(90, 90, 90)
	btn.TextColor3 = Color3.new(1, 1, 1)
	btn.Parent = panel
	return btn
end

-- Buttons (layout preserved)
local autoLockBtn = createButton("AutoLock (Blyat)", 40)
local speedBoostBtn = createButton("SpeedBoost (Blyat)", 80)
local treeBtn = createButton("Tree (Blyat)", 120)
local boomBtn = createButton("Boom", 160)
local bomboxBtn = createButton("Bombox (Blyat)", 200)
local flyBtn = createButton("La Godderness (Blyat)", 240)
local shutdownBtn = createButton("Goodnight Bomba", 280)

-- Menu toggle with animation and sound
menuButton.MouseButton1Click:Connect(function()
	if not panel.Visible then
		-- Play open sound
		sound:Play()

		-- Animation: fade and slide
		panel.Visible = true
		panel.Position = UDim2.new(0.5, -120, 0.5, 0)
		panel.BackgroundTransparency = 1
		for i = 0, 1, 0.1 do
			panel.BackgroundTransparency = 1 - i
			panel.Position = UDim2.new(0.5, -120, 0.5, -175 * i)
			task.wait(0.01)
		end
	else
		panel.Visible = false
	end
end)

-- AutoLock
local autoLockOn = false
local autoLockLoop
autoLockBtn.MouseButton1Click:Connect(function()
	autoLockOn = not autoLockOn
	autoLockBtn.Text = "AutoLock (" .. (autoLockOn and "Suka" or "Blyat") .. ")"
	if autoLockOn then
		autoLockLoop = task.spawn(function()
			local cam = workspace.CurrentCamera
			while autoLockOn do
				local closest, dist = nil, math.huge
				for _, p in ipairs(game.Players:GetPlayers()) do
					if p ~= player and p.Character and p.Character:FindFirstChild("HumanoidRootPart") then
						local d = (player.Character.HumanoidRootPart.Position - p.Character.HumanoidRootPart.Position).Magnitude
						if d < dist then
							dist = d
							closest = p
						end
					end
				end
				if closest and closest.Character then
					cam.CFrame = CFrame.new(cam.CFrame.Position, closest.Character.HumanoidRootPart.Position)
				end
				task.wait(0.1)
			end
		end)
	else
		task.cancel(autoLockLoop)
	end
end)

-- SpeedBoost
local speedOn = false
speedBoostBtn.MouseButton1Click:Connect(function()
	local humanoid = player.Character and player.Character:FindFirstChild("Humanoid")
	if humanoid then
		speedOn = not speedOn
		humanoid.WalkSpeed = speedOn and 100 or 16
		speedBoostBtn.Text = "SpeedBoost (" .. (speedOn and "Suka" or "Blyat") .. ")"
	end
end)

-- Tree teleport toggle
local treeOn = false
local treeLoop
treeBtn.MouseButton1Click:Connect(function()
	treeOn = not treeOn
	treeBtn.Text = "Tree (" .. (treeOn and "Suka" or "Blyat") .. ")"
	if treeOn then
		treeLoop = task.spawn(function()
			local hrp = player.Character:WaitForChild("HumanoidRootPart")
			while treeOn do
				local pos = Vector3.new(math.random(-250, 250), 100, math.random(-250, 250))
				hrp.CFrame = CFrame.new(pos)
				task.wait(0.3)
			end
		end)
	else
		task.cancel(treeLoop)
	end
end)

-- Boom (kill self)
boomBtn.MouseButton1Click:Connect(function()
	local humanoid = player.Character and player.Character:FindFirstChild("Humanoid")
	if humanoid then
		humanoid.Health = 0
	end
end)

-- Bombox (purple hitboxes)
local bomboxOn = false
bomboxBtn.MouseButton1Click:Connect(function()
	bomboxOn = not bomboxOn
	bomboxBtn.Text = "Bombox (" .. (bomboxOn and "Suka" or "Blyat") .. ")"
	for _, p in ipairs(game.Players:GetPlayers()) do
		if p.Character then
			for _, part in ipairs(p.Character:GetDescendants()) do
				if part:IsA("BasePart") then
					if bomboxOn then
						local box = Instance.new("BoxHandleAdornment")
						box.Name = "HitboxVis"
						box.Adornee = part
						box.AlwaysOnTop = true
						box.ZIndex = 10
						box.Size = part.Size
						box.Color3 = Color3.fromRGB(128, 0, 128)
						box.Transparency = 0.3
						box.Parent = part
					else
						local adorn = part:FindFirstChild("HitboxVis")
						if adorn then adorn:Destroy() end
					end
				end
			end
		end
	end
end)

-- La Godderness (float up slowly)
local flyOn = false
local flyLoop
flyBtn.MouseButton1Click:Connect(function()
	flyOn = not flyOn
	flyBtn.Text = "La Godderness (" .. (flyOn and "Suka" or "Blyat") .. ")"
	local hrp = player.Character and player.Character:FindFirstChild("HumanoidRootPart")
	if flyOn and hrp then
		flyLoop = task.spawn(function()
			while flyOn and hrp do
				hrp.Velocity = Vector3.new(0, 20, 0)
				task.wait(0.1)
			end
		end)
	else
		if hrp then hrp.Velocity = Vector3.zero end
		task.cancel(flyLoop)
	end
end)

-- Goodnight Bomba (kick)
shutdownBtn.MouseButton1Click:Connect(function()
	player:Kick("Goodnight Bomba")
end)
