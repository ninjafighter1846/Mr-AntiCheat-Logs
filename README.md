--====================================================================================================================================
-- MR ANTICHEAT LOGS (ADMIN POWER-CMDS EDITION)
--====================================================================================================================================
local Players = game:GetService("Players")
local TweenService = game:GetService("TweenService")
local RunService = game:GetService("RunService")
local UserInputService = game:GetService("UserInputService")
local LogService = game:GetService("LogService")

local localPlayer = Players.LocalPlayer
local playerGui = localPlayer:WaitForChild("PlayerGui")
local randomName = "MAC_" .. tostring(math.random(100000, 999999))

-- Altes GUI säubern
for _, child in pairs(playerGui:GetChildren()) do
	if child:IsA("ScreenGui") and (child.Name == "MrAntiCheat_MasterSuite" or string.match(child.Name, "^MAC_")) then
		child:Destroy()
	end
end

-- 1. BASE DESIGN INTERFACE
local ScreenGui = Instance.new("ScreenGui")
ScreenGui.Name = randomName
ScreenGui.ResetOnSpawn = false
ScreenGui.Parent = playerGui

local MainFrame = Instance.new("Frame")
MainFrame.Size = UDim2.new(0, 440, 0, 340)
MainFrame.Position = UDim2.new(1, -455, 0.5, -170)
MainFrame.BackgroundColor3 = Color3.fromRGB(10, 11, 14)
MainFrame.BackgroundTransparency = 0.05
MainFrame.Active = true
MainFrame.Draggable = true
MainFrame.Parent = ScreenGui

local MainCorner = Instance.new("UICorner")
MainCorner.CornerRadius = UDim.new(0, 8)
MainCorner.Parent = MainFrame

local MainStroke = Instance.new("UIStroke")
MainStroke.Thickness = 1.5
MainStroke.Color = Color3.fromRGB(0, 180, 255)
MainStroke.Parent = MainFrame

-- Titel-Leiste
local TitleFrame = Instance.new("Frame")
TitleFrame.Size = UDim2.new(1, 0, 0, 35)
TitleFrame.BackgroundColor3 = Color3.fromRGB(15, 17, 22)
TitleFrame.BorderSizePixel = 0
TitleFrame.Parent = MainFrame

local TitleLabel = Instance.new("TextLabel")
TitleLabel.Size = UDim2.new(1, -20, 1, 0)
TitleLabel.Position = UDim2.new(0, 15, 0, 0)
TitleLabel.BackgroundTransparency = 1
TitleLabel.Text = "🛡️ Mr AntiCheat Logs (F2 für Power-Cmds)"
TitleLabel.TextColor3 = Color3.fromRGB(255, 255, 255)
TitleLabel.Font = Enum.Font.GothamBold
TitleLabel.TextSize = 13
TitleLabel.TextXAlignment = Enum.TextXAlignment.Left
TitleLabel.Parent = TitleFrame

local TitleCorner = Instance.new("UICorner")
TitleCorner.CornerRadius = UDim.new(0, 8)
TitleCorner.Parent = TitleFrame

-- Log-Liste (Scrolling)
local LogList = Instance.new("ScrollingFrame")
LogList.Size = UDim2.new(1, -20, 1, -110)
LogList.Position = UDim2.new(0, 10, 0, 45)
LogList.BackgroundTransparency = 1
LogList.BorderSizePixel = 0
LogList.CanvasSize = UDim2.new(0, 0, 0, 0)
LogList.ScrollBarThickness = 2
LogList.ScrollBarImageColor3 = Color3.fromRGB(0, 180, 255)
LogList.Parent = MainFrame

local UIListLayout = Instance.new("UIListLayout")
UIListLayout.SortOrder = Enum.SortOrder.LayoutOrder
UIListLayout.Padding = UDim.new(0, 4)
UIListLayout.Parent = LogList

-- COMMAND LINE (HD-Style Bar)
local CmdFrame = Instance.new("Frame")
CmdFrame.Size = UDim2.new(1, -20, 0, 25)
CmdFrame.Position = UDim2.new(0, 10, 1, -60)
CmdFrame.BackgroundColor3 = Color3.fromRGB(20, 22, 30)
CmdFrame.Visible = false
CmdFrame.Parent = MainFrame

local CmdCorner = Instance.new("UICorner")
CmdCorner.CornerRadius = UDim.new(0, 4)
CmdCorner.Parent = CmdFrame

local CmdBox = Instance.new("TextBox")
CmdBox.Size = UDim2.new(1, -10, 1, 0)
CmdBox.Position = UDim2.new(0, 5, 0, 0)
CmdBox.BackgroundTransparency = 1
CmdBox.Text = ""
CmdBox.PlaceholderText = "Befehl eingeben... (;btools, ;tptool, ;tp [Name], ;speed [X])"
CmdBox.TextColor3 = Color3.fromRGB(255, 255, 255)
CmdBox.PlaceholderColor3 = Color3.fromRGB(100, 105, 120)
CmdBox.Font = Enum.Font.Code
CmdBox.TextSize = 12
CmdBox.TextXAlignment = Enum.TextXAlignment.Left
CmdBox.Parent = CmdFrame

-- Statistik-Leiste unten
local StatsFrame = Instance.new("Frame")
StatsFrame.Size = UDim2.new(1, -20, 0, 25)
StatsFrame.Position = UDim2.new(0, 10, 1, -30)
StatsFrame.BackgroundColor3 = Color3.fromRGB(15, 17, 22)
StatsFrame.Parent = MainFrame

local StatsCorner = Instance.new("UICorner")
StatsCorner.CornerRadius = UDim.new(0, 4)
StatsCorner.Parent = StatsFrame

local StatsLabel = Instance.new("TextLabel")
StatsLabel.Size = UDim2.new(1, -20, 1, 0)
StatsLabel.Position = UDim2.new(0, 10, 0, 0)
StatsLabel.BackgroundTransparency = 1
StatsLabel.Text = "Analysiere Umgebung..."
StatsLabel.TextColor3 = Color3.fromRGB(140, 145, 160)
StatsLabel.Font = Enum.Font.SourceSansBold
StatsLabel.TextSize = 12
StatsLabel.TextXAlignment = Enum.TextXAlignment.Left
StatsLabel.Parent = StatsFrame

--====================================================================================================================================
-- SYSTEM-LOGIK & ERWEITERTE HACKS/COMMANDS
--====================================================================================================================================
local playerData = {}
local totalFlags = 0
local loggedLines = 0
local espActive = true
local noclipActive = false
local allLogHistory = ""

local function addLog(message, color, tag)
	loggedLines = loggedLines + 1
	allLogHistory = allLogHistory .. "[" .. os.date("%X") .. "] " .. (tag or "[INFO]") .. " " .. message .. "\n"
	
	local row = Instance.new("Frame")
	row.Size = UDim2.new(1, -5, 0, 22)
	row.BackgroundColor3 = Color3.fromRGB(18, 20, 26)
	row.BackgroundTransparency = 0.4
	row.BorderSizePixel = 0
	row.Parent = LogList
	
	local rc = Instance.new("UICorner")
	rc.CornerRadius = UDim.new(0, 4)
	rc.Parent = row
	
	local tl = Instance.new("TextLabel")
	tl.Size = UDim2.new(0, 55, 1, 0)
	tl.Position = UDim2.new(0, 6, 0, 0)
	tl.BackgroundTransparency = 1
	tl.Text = "[" .. os.date("%X") .. "]"
	tl.TextColor3 = Color3.fromRGB(90, 95, 110)
	tl.Font = Enum.Font.Code
	tl.TextSize = 11
	tl.Parent = row
	
	local tg = Instance.new("TextLabel")
	tg.Size = UDim2.new(0, 75, 1, 0)
	tg.Position = UDim2.new(0, 65, 0, 0)
	tg.BackgroundTransparency = 1
	tg.Text = tag or "[INFO]"
	tg.TextColor3 = color
	tg.Font = Enum.Font.GothamBold
	tg.TextSize = 10
	tg.Parent = row
	
	local txt = Instance.new("TextLabel")
	txt.Size = UDim2.new(1, -150, 1, 0)
	txt.Position = UDim2.new(0, 145, 0, 0)
	txt.BackgroundTransparency = 1
	txt.Text = message
	txt.TextColor3 = Color3.fromRGB(230, 230, 240)
	txt.Font = Enum.Font.Gotham
	txt.TextSize = 11
	txt.TextXAlignment = Enum.TextXAlignment.Left
	txt.Parent = row
	
	LogList.CanvasSize = UDim2.new(0, 0, 0, UIListLayout.AbsoluteContentSize.Y + 10)
	LogList.CanvasPosition = Vector2.new(0, LogList.AbsoluteCanvasSize.Y)
	
	if tag ~= "[INFO]" and tag ~= "[SYSTEM]" and tag ~= "[JOIN]" and tag ~= "[LEAVE]" then 
		totalFlags = totalFlags + 1 
	end
end

-- Hilfsfunktion um Spieler via Kürzel zu finden
local function findTarget(namePart)
	if not namePart then return nil end
	for _, p in pairs(Players:GetPlayers()) do
		if string.find(string.lower(p.Name), string.lower(namePart)) or string.find(string.lower(p.DisplayName), string.lower(namePart)) then
			return p
		end
	end
	return nil
end

-- F2 Open/Close Listener
UserInputService.InputBegan:Connect(function(input, processed)
	if processed then return end
	if input.KeyCode == Enum.KeyCode.F2 then
		CmdFrame.Visible = not CmdFrame.Visible
		if CmdFrame.Visible then
			CmdBox:CaptureFocus()
		end
	end
end)

-- DER ERWEITERTE BEFEHLS-PARSER
CmdBox.FocusLost:Connect(function(enterPressed)
	if not enterPressed then return end
	local fullText = CmdBox.Text
	CmdBox.Text = ""
	CmdFrame.Visible = false
	
	local args = string.split(string.lower(fullText), " ")
	local cmd = args[1]
	
	-- 1. CLEAN LOGS
	if cmd == ";cls" or cmd == ";clear" then
		for _, child in pairs(LogList:GetChildren()) do
			if child:IsA("Frame") then child:Destroy() end
		end
		addLog("Logs bereinigt.", Color3.fromRGB(255, 255, 255), "[EXECUTE]")
		
	-- 2. ESP CONTROLS
	elseif cmd == ";esp" then
		if args[2] == "false" then
			espActive = false
			addLog("Radar-ESP deaktiviert.", Color3.fromRGB(255, 210, 0), "[EXECUTE]")
		else
			espActive = true
			addLog("Radar-ESP aktiviert.", Color3.fromRGB(0, 255, 150), "[EXECUTE]")
		end
		
	-- 3. RESET CHARACTER
	elseif cmd == ";reset" then
		if localPlayer.Character and localPlayer.Character:FindFirstChild("Humanoid") then
			localPlayer.Character.Humanoid.Health = 0
			addLog("Charakter zurückgesetzt.", Color3.fromRGB(255, 80, 80), "[EXECUTE]")
		end

	-- 4. BTOOLS INJEKTION
	elseif cmd == ";btools" then
		local backpack = localPlayer:WaitForChild("Backpack")
		local t1, t2, t3, t4 = Instance.new("HopperBin"), Instance.new("HopperBin"), Instance.new("HopperBin"), Instance.new("HopperBin")
		t1.BinType, t2.BinType, t3.BinType, t4.BinType = 1, 2, 3, 4
		t1.Parent, t2.Parent, t3.Parent, t4.Parent = backpack, backpack, backpack, backpack
		addLog("BTools erfolgreich ins Inventar gelegt!", Color3.fromRGB(200, 50, 255), "[BTOOLS]")

	-- 5. TP TOOL GENERATOR
	elseif cmd == ";tptool" then
		local backpack = localPlayer:WaitForChild("Backpack")
		local tool = Instance.new("Tool")
		tool.Name = "Teleport-Klicker"
		tool.RequiresHandle = false
		tool.Activated:Connect(function()
			local mouse = localPlayer:GetMouse()
			if localPlayer.Character and localPlayer.Character:FindFirstChild("HumanoidRootPart") and mouse.Target then
				localPlayer.Character.HumanoidRootPart.CFrame = CFrame.new(mouse.Hit.Position + Vector3.new(0, 3, 0))
			end
		end)
		tool.Parent = backpack
		addLog("TP-Tool ausgerüstet! Klicken zum Teleportieren.", Color3.fromRGB(0, 255, 255), "[TP-TOOL]")

	-- 6. DIRECT TELEPORT (TO PLAYER)
	elseif cmd == ";tp" then
		local target = findTarget(args[2])
		if target and target.Character and target.Character:FindFirstChild("HumanoidRootPart") then
			if localPlayer.Character and localPlayer.Character:FindFirstChild("HumanoidRootPart") then
				localPlayer.Character.HumanoidRootPart.CFrame = target.Character.HumanoidRootPart.CFrame * CFrame.new(0, 2, -2)
				addLog("Zu " .. target.Name .. " teleportiert.", Color3.fromRGB(0, 180, 255), "[EXECUTE]")
			end
		else
			addLog("Spieler nicht gefunden!", Color3.fromRGB(255, 50, 50), "[ERROR]")
		end

	-- 7. BRING PLAYER (CLIENT PHYSICS)
	elseif cmd == ";bring" then
		local target = findTarget(args[2])
		if target and target.Character and target.Character:FindFirstChild("HumanoidRootPart") then
			if localPlayer.Character and localPlayer.Character:FindFirstChild("HumanoidRootPart") then
				target.Character.HumanoidRootPart.CFrame = localPlayer.Character.HumanoidRootPart.CFrame * CFrame.new(0, 0, -3)
				addLog("Versuche " .. target.Name .. " zu bringen.", Color3.fromRGB(255, 140, 0), "[EXECUTE]")
			end
		else
			addLog("Spieler nicht gefunden!", Color3.fromRGB(255, 50, 50), "[ERROR]")
		end

	-- 8. SPEED CHANGER
	elseif cmd == ";speed" or cmd == ";ws" then
		local val = tonumber(args[2]) or 16
		if localPlayer.Character and localPlayer.Character:FindFirstChild("Humanoid") then
			localPlayer.Character.Humanoid.WalkSpeed = val
			addLog("Laufgeschwindigkeit auf " .. val .. " gesetzt.", Color3.fromRGB(50, 255, 50), "[EXECUTE]")
		end

	-- 9. JUMPPOWER CHANGER
	elseif cmd == ";jumppower" or cmd == ";jp" then
		local val = tonumber(args[2]) or 50
		if localPlayer.Character and localPlayer.Character:FindFirstChild("Humanoid") then
			localPlayer.Character.Humanoid.UseJumpPower = true
			localPlayer.Character.Humanoid.JumpPower = val
			addLog("Sprungkraft auf " .. val .. " gesetzt.", Color3.fromRGB(50, 255, 50), "[EXECUTE]")
		end

	-- 10. NOCLIP MODUS
	elseif cmd == ";noclip" then
		noclipActive = true
		addLog("Noclip aktiviert.", Color3.fromRGB(0, 255, 255), "[EXECUTE]")
		
	elseif cmd == ";clip" then
		noclipActive = false
		addLog("Noclip deaktiviert.", Color3.fromRGB(255, 100, 0), "[EXECUTE]")
		
	-- 11. REJOIN
	elseif cmd == ";rejoin" then
		addLog("Verbinde neu...", Color3.fromRGB(255, 255, 0), "[SYSTEM]")
		game:GetService("TeleportService"):Teleport(game.PlaceId, localPlayer)
		
	-- 12. LOAD INFINITE YIELD AS BACKUP
	elseif cmd == ";infyield" or cmd == ";iy" then
		addLog("Lade Infinite Yield Admin...", Color3.fromRGB(255, 255, 255), "[SYSTEM]")
		loadstring(game:HttpGet('https://raw.githubusercontent.com/EdgeYolo/InfiniteYield/master/source'))()

	-- 13. REKORDIERTE LOGS KOPIEREN
	elseif cmd == ";copy" then
		if setclipboard then
			setclipboard(allLogHistory)
			addLog("Logs in Zwischenablage kopiert!", Color3.fromRGB(150, 255, 150), "[EXECUTE]")
		else
			addLog("Executor blockiert 'setclipboard'!", Color3.fromRGB(255, 50, 50), "[ERROR]")
		end
	else
		addLog("Unbefugter/Unbekannter Befehl!", Color3.fromRGB(255, 80, 80), "[ERROR]")
	end
end)

-- Realtime Noclip Engine Loop
RunService.Stepped:Connect(function()
	if noclipActive and localPlayer.Character then
		for _, part in pairs(localPlayer.Character:GetDescendants()) do
			if part:IsA("BasePart") then
				part.CanCollide = false
			end
		end
	end
end)

--====================================================================================================================================
-- DETEKTIONS KERN- ENGINE
--====================================================================================================================================
local function monitor(player)
	if player == localPlayer then return end
	
	player.Chatted:Connect(function(msg)
		local l = string.lower(msg)
		if string.find(l, "cheat") or string.find(l, "exploit") or string.find(l, "hack") or string.find(l, "vape") then
			addLog(player.Name .. ": '" .. msg .. "'", Color3.fromRGB(255, 165, 0), "[CHAT-FLAG]")
		else
			addLog(player.Name .. ": " .. msg, Color3.fromRGB(140, 160, 180), "[CHAT]")
		end
	end)
	
	player.CharacterAdded:Connect(function(char)
		local root = char:WaitForChild("HumanoidRootPart", 10)
		local hum = char:WaitForChild("Humanoid", 10)
		if not root or not hum then return end
		
		playerData[player.UserId] = { lastPos = root.Position, airTime = 0 }
		
		local head = char:WaitForChild("Head", 5)
		if head then
			if head:FindFirstChild("ARadar") then head["ARadar"]:Destroy() end
			local bg = Instance.new("BillboardGui")
			bg.Name = "ARadar"
			bg.AlwaysOnTop = true
			bg.Size = UDim2.new(0, 140, 0, 30)
			bg.Adornee = head
			bg.ExtentsOffset = Vector3.new(0, 3, 0)
			bg.Parent = head
			
			local el = Instance.new("TextLabel")
			el.Size = UDim2.new(1, 0, 1, 0)
			el.BackgroundTransparency = 1
			el.Text = player.Name
			el.TextColor3 = Color3.fromRGB(0, 255, 150)
			el.Font = Enum.Font.GothamBold
			el.TextSize = 11
			el.Parent = bg
			playerData[player.UserId].esp = el
		end
		
		task.spawn(function()
			while char and char.Parent and root and hum and task.wait(1) do
				local d = playerData[player.UserId]
				if not d then break end
				
				local cPos = root.Position
				local delta = (cPos - d.lastPos).Magnitude
				local vel = root.AssemblyLinearVelocity
				
				if d.esp then d.esp.Visible = espActive end
				
				if delta > 45 and hum.WalkSpeed <= 22 then
					addLog(player.Name .. " verschoben um " .. math.floor(delta) .. " Studs", Color3.fromRGB(255, 30, 30), "[SPEED]")
					if d.esp then d.esp.TextColor3 = Color3.fromRGB(255, 30, 30) end
				end
				
				local st = hum:GetState()
				if st == Enum.HumanoidStateType.Freefall then
					d.airTime = d.airTime + 1
					if d.airTime > 3 and vel.Y > -4 and vel.Y < 4 then
						addLog(player.Name .. " fliegt/schwebt!", Color3.fromRGB(255, 110, 0), "[FLIGHT]")
						if d.esp then d.esp.TextColor3 = Color3.fromRGB(255, 110, 0) end
					end
				else
					d.airTime = 0
					if d.esp and delta <= 45 then d.esp.TextColor3 = Color3.fromRGB(0, 255, 150) end
				end
				
				local lChar = localPlayer.Character
				if lChar and lChar:FindFirstChild("HumanoidRootPart") and d.esp then
					local realDist = (lChar.HumanoidRootPart.Position - cPos).Magnitude
					d.esp.Text = player.Name .. " [" .. math.floor(realDist) .. "m]"
				end
				
				d.lastPos = cPos
			end
		end)
	end)
end

--====================================================================================================================================
-- INITIALISIERUNG
--====================================================================================================================================
for _, p in pairs(Players:GetPlayers()) do monitor(p) end
Players.PlayerAdded:Connect(function(p) addLog(p.Name .. " beigetreten.", Color3.fromRGB(50, 220, 110), "[JOIN]") monitor(p) end)
Players.PlayerRemoving:Connect(function(p) playerData[p.UserId] = nil addLog(p.Name .. " verlassen.", Color3.fromRGB(130, 135, 145), "[LEAVE]") end)

task.spawn(function()
	while task.wait(0.5) do
		local plCount = #Players:GetPlayers()
		local fps = math.floor(1 / RunService.RenderStepped:Wait())
		StatsLabel.Text = "Spieler: " .. plCount .. " | FPS: " .. fps .. " | Flags: " .. totalFlags .. " | Logs: " .. loggedLines
	end
end)

addLog("System bereit. F2 -> Admin-Terminal geöffnet.", Color3.fromRGB(0, 190, 255), "[SYSTEM]")
