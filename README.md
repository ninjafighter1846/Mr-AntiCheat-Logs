--====================================================================================================================================
-- MR. ANTICHEAT LOGS - ULTIMATE MASSIVE EDITION (2026)
--====================================================================================================================================
local Players, CoreGui, TweenService, RunService, Workspace, LogService = game:GetService("Players"), game:GetService("CoreGui"), game:GetService("TweenService"), game:GetService("RunService"), game:GetService("Workspace"), game:GetService("LogService")
if CoreGui:FindFirstChild("MrAntiCheat_MasterSuite") then CoreGui["MrAntiCheat_MasterSuite"]:Destroy() end
local ScreenGui = Instance.new("ScreenGui") ScreenGui.Name = "MrAntiCheat_MasterSuite" ScreenGui.ResetOnSpawn = false ScreenGui.Parent = CoreGui
local MainFrame = Instance.new("Frame") MainFrame.Size = UDim2.new(0, 480, 0, 360) MainFrame.Position = UDim2.new(1, -495, 0.5, -180) MainFrame.BackgroundColor3 = Color3.fromRGB(8, 9, 13) MainFrame.BackgroundTransparency = 0.05 MainFrame.Active = true MainFrame.Draggable = true MainFrame.Parent = ScreenGui
local MainCorner, MainStroke = Instance.new("UICorner"), Instance.new("UIStroke") MainCorner.CornerRadius = UDim.new(0, 12) MainCorner.Parent = MainFrame MainStroke.Thickness = 1.8 MainStroke.Color = Color3.fromRGB(0, 215, 255) MainStroke.Parent = MainFrame
local TitleFrame = Instance.new("Frame") TitleFrame.Size = UDim2.new(1, 0, 0, 40) TitleFrame.BackgroundColor3 = Color3.fromRGB(14, 16, 22) TitleFrame.BorderSizePixel = 0 TitleFrame.Parent = MainFrame
local TitleLabel = Instance.new("TextLabel") TitleLabel.Size = UDim2.new(1, -20, 1, 0) TitleLabel.Position = UDim2.new(0, 15, 0, 0) TitleLabel.BackgroundTransparency = 1 TitleLabel.Text = "🛡️ Mr AntiCheat Logs" TitleLabel.TextColor3 = Color3.fromRGB(255, 255, 255) TitleLabel.Font = Enum.Font.GothamBold TitleLabel.TextSize = 13 TitleLabel.TextXAlignment = Enum.TextXAlignment.Left TitleLabel.Parent = TitleFrame
local TitleCorner = Instance.new("UICorner") TitleCorner.CornerRadius = UDim.new(0, 12) TitleCorner.Parent = TitleFrame
local LogList = Instance.new("ScrollingFrame") LogList.Size = UDim2.new(1, -20, 1, -85) LogList.Position = UDim2.new(0, 10, 0, 50) LogList.BackgroundTransparency = 1 LogList.BorderSizePixel = 0 LogList.CanvasSize = UDim2.new(0, 0, 0, 0) LogList.ScrollBarThickness = 3 LogList.ScrollBarImageColor3 = Color3.fromRGB(0, 215, 255) LogList.Parent = MainFrame
local UIListLayout = Instance.new("UIListLayout") UIListLayout.SortOrder = Enum.SortOrder.LayoutOrder UIListLayout.Padding = UDim.new(0, 3) UIListLayout.Parent = LogList
local StatsFrame = Instance.new("Frame") StatsFrame.Size = UDim2.new(1, -20, 0, 25) StatsFrame.Position = UDim2.new(0, 10, 1, -30) StatsFrame.BackgroundColor3 = Color3.fromRGB(14, 16, 22) StatsFrame.Parent = MainFrame
local StatsCorner = Instance.new("UICorner") StatsCorner.CornerRadius = UDim.new(0, 6) StatsCorner.Parent = StatsFrame
local StatsLabel = Instance.new("TextLabel") StatsLabel.Size = UDim2.new(1, -20, 1, 0) StatsLabel.Position = UDim2.new(0, 10, 0, 0) StatsLabel.BackgroundTransparency = 1 StatsLabel.Text = "Lade Kern-Sensoren..." StatsLabel.TextColor3 = Color3.fromRGB(150, 160, 180) StatsLabel.Font = Enum.Font.SourceSansBold StatsLabel.TextSize = 12 StatsLabel.TextXAlignment = Enum.TextXAlignment.Left StatsLabel.Parent = StatsFrame

--====================================================================================================================================
-- KERN-LOGIK & ERWEITERTE DATEN-STRUKTUREN
--====================================================================================================================================
local pData, totalDetections, loggedLines = {}, 0, 0
local function addLog(msg, clr, tag)
	loggedLines = loggedLines + 1
	local row = Instance.new("Frame") row.Size = UDim2.new(1, -5, 0, 24) row.BackgroundColor3 = Color3.fromRGB(16, 18, 26) row.BackgroundTransparency = 0.5 row.BorderSizePixel = 0 row.Parent = LogList
	local rc = Instance.new("UICorner") rc.CornerRadius = UDim.new(0, 4) rc.Parent = row
	local tl = Instance.new("TextLabel") tl.Size = UDim2.new(0, 55, 1, 0) tl.Position = UDim2.new(0, 6, 0, 0) tl.BackgroundTransparency = 1 tl.Text = "["..os.date("%X").."]" tl.TextColor3 = Color3.fromRGB(90, 95, 110) tl.Font = Enum.Font.Code tl.TextSize = 11 tl.Parent = row
	local tg = Instance.new("TextLabel") tg.Size = UDim2.new(0, 75, 1, 0) tg.Position = UDim2.new(0, 65, 0, 0) tg.BackgroundTransparency = 1 tg.Text = tag or "[INFO]" tg.TextColor3 = clr tg.Font = Enum.Font.GothamBold tg.TextSize = 10 tg.Parent = row
	local txt = Instance.new("TextLabel") txt.Size = UDim2.new(1, -150, 1, 0) txt.Position = UDim2.new(0, 145, 0, 0) txt.BackgroundTransparency = 1 txt.Text = msg txt.TextColor3 = Color3.fromRGB(240, 240, 250) txt.Font = Enum.Font.Gotham txt.TextSize = 11 txt.TextXAlignment = Enum.TextXAlignment.Left txt.Parent = row
	LogList.CanvasSize = UDim2.new(0, 0, 0, UIListLayout.AbsoluteContentSize.Y + 10)
	LogList.CanvasPosition = Vector2.new(0, LogList.AbsoluteCanvasSize.Y)
	if tag ~= "[INFO]" and tag ~= "[SYSTEM]" and tag ~= "[JOIN]" and tag ~= "[LEAVE]" then totalDetections = totalDetections + 1 end
end

--====================================================================================================================================
-- MASSIVE UNTERSTÜTZUNG FÜR PACKETS, ANALYTICS & CHAT-SENSING
--====================================================================================================================================
local function monitor(p)
	if p == Players.LocalPlayer then return end
	p.Chatted:Connect(function(msg)
		local l = string.lower(msg)
		if string.find(l, "cheat") or string.find(l, "exploit") or string.find(l, "hack") or string.find(l, "bypass") or string.find(l, "vape") or string.find(l, "synapse") then
			addLog(p.Name..": '"..msg.."'", Color3.fromRGB(255, 165, 0), "[CHAT-FLAG]")
		else
			addLog(p.Name..": "..msg, Color3.fromRGB(140, 160, 180), "[CHAT]")
		end
	end)
	
	p.CharacterAdded:Connect(function(char)
		local root = char:WaitForChild("HumanoidRootPart", 12)
		local hum = char:WaitForChild("Humanoid", 12)
		if not root or not hum then addLog(p.Name.." Root/Humanoid Timeout!", Color3.fromRGB(255, 100, 0), "[TIMEOUT]") return end
		
		pData[p.UserId] = {lastPos = root.Position, airTime = 0, lastH = hum.Health, noClipWarns = 0, toolCount = 0, lastVelocity = Vector3.new(0,0,0)}
		addLog(p.Name.." Charakter geladen (Physik verknüpft)", Color3.fromRGB(0, 255, 150), "[CHAR-LOAD]")
		
		-- ESP-Injektion
		local h = char:WaitForChild("Head", 5)
		if h then
			if h:FindFirstChild("ARadar") then h["ARadar"]:Destroy() end
			local bg = Instance.new("BillboardGui") bg.Name = "ARadar" bg.AlwaysOnTop = true bg.Size = UDim2.new(0, 140, 0, 30) bg.Adornee = h bg.ExtentsOffset = Vector3.new(0, 3, 0) bg.Parent = h
			local el = Instance.new("TextLabel") el.Size = UDim2.new(1, 0, 1, 0) el.BackgroundTransparency = 1 el.Text = p.Name el.TextColor3 = Color3.fromRGB(0, 255, 150) el.Font = Enum.Font.GothamBold el.TextSize = 11 el.Parent = bg
			pData[p.UserId].esp = el
		end

		char.ChildAdded:Connect(function(child)
			if child:IsA("Tool") then
				addLog(p.Name.." hat Tool ausgerüstet: "..child.Name, Color3.fromRGB(190, 100, 255), "[TOOL-EQUIP]")
			end
		end)
		
		-- ENDLOSE PHYSEK-EVALUATIONSSCHLEIFE
		task.spawn(function()
			while char and char.Parent and root and hum and task.wait(0.8) do
				local d = pData[p.UserId] if not d then break end
				local cPos = root.Position
				local delta = (cPos - d.lastPos).Magnitude
				local vel = root.AssemblyLinearVelocity
				local speed = root.AssemblyLinearVelocity.Magnitude
				
				-- 1. SPEEDHACK & TELEPORTATION EXTRACTION
				if delta > 38 and hum.WalkSpeed <= 22 then
					addLog(string.format("%s verlegte Position um %d Studs!", p.Name, delta), Color3.fromRGB(255, 30, 30), "[DET-SPEED]")
					if d.esp then d.esp.TextColor3 = Color3.fromRGB(255,30,30) end
				end
				
				-- 2. FLYING / FREEFALL ANOMALIES
				local st = hum:GetState()
				if st == Enum.HumanoidStateType.Freefall or st == Enum.HumanoidStateType.FallingDown then
					d.airTime = d.airTime + 0.8
					if d.airTime > 3.5 and vel.Y > -4 and vel.Y < 4 then
						addLog(p.Name.." schwebt seit "..math.floor(d.airTime).."s! (Y-Velo: "..math.floor(vel.Y)..")", Color3.fromRGB(255, 110, 0), "[DET-FLIGHT]")
						if d.esp then d.esp.TextColor3 = Color3.fromRGB(255,110,0) end
					end
				else
					d.airTime = 0
					if d.esp and delta <= 38 then d.esp.TextColor3 = Color3.fromRGB(0, 255, 150) end
				end
				
				-- 3. NOCLIP DETECTION ENGINE
				if speed > 80 and delta < 2 then
					d.noClipWarns = d.noClipWarns + 1
					if d.noClipWarns >= 2 then
						addLog(p.Name.." kollidiert unnatürlich mit Objekten!", Color3.fromRGB(255, 210, 0), "[DET-NOCLIP]")
					end
				else
					d.noClipWarns = math.max(0, d.noClipWarns - 1)
				end
				
				-- 4. TELEPORT DOWN / VOID GODMODE
				if cPos.Y < -80 and hum.Health == hum.MaxHealth then
					addLog(p.Name.." überlebt kritische Tiefe ("..math.floor(cPos.Y).."Y) ohne Schaden!", Color3.fromRGB(230, 0, 255), "[DET-VOID]")
				end
				
				-- 5. SEVERE VELOCITY SPIKES (LAUNCHERS)
				if vel.Magnitude > 320 then
					addLog(p.Name.." extreme Beschleunigung gemessen: "..math.floor(vel.Magnitude).." Velo!", Color3.fromRGB(255, 0, 100), "[DET-VELO]")
				end
				
				-- 6. INTERNE DISTANZ-BERECHNUNG FÜR RADAR-TEXT
				local lChar = Players.LocalPlayer.Character
				if lChar and lChar:FindFirstChild("HumanoidRootPart") and d.esp then
					local realDist = (lChar.HumanoidRootPart.Position - cPos).Magnitude
					d.esp.Text = string.format("%s [%d Studs]", p.Name, math.floor(realDist))
				end
				
				d.lastPos = cPos d.lastH = hum.Health d.lastVelocity = vel
			end
		end)
	end)
end

--====================================================================================================================================
-- INITIALISIERUNG & DYNAMISCHE METADATEN-AKTUALISIERUNG
--====================================================================================================================================
for _, p in pairs(Players:GetPlayers()) do monitor(p) end
Players.PlayerAdded:Connect(function(p) addLog(p.Name.." ["..p.UserId.."] verbunden.", Color3.fromRGB(50, 220, 110), "[JOIN]") monitor(p) end)
Players.PlayerRemoving:Connect(function(p) pData[p.UserId] = nil addLog(p.Name.." Verbindung getrennt.", Color3.fromRGB(130, 135, 145), "[LEAVE]") end)

-- Echtzeit Server-Ressourcen Tracker in der Fußzeile
task.spawn(function()
	while task.wait(0.5) do
		local plCount = #Players:GetPlayers()
		local fps = math.floor(1 / RunService.RenderStepped:Wait())
		StatsLabel.Text = string.format("Spieler: %d | FPS: %d | Flags: %d | Logs: %d", plCount, fps, totalDetections, loggedLines)
	end
end)

addLog("Sicherheitsmatrix initialisiert. Alle Sensoren online.", Color3.fromRGB(0, 190, 255), "[SYSTEM]")
addLog("Überwache global: Speed, Fly, Noclip, Chat-Exploits, Teleports.", Color3.fromRGB(0, 190, 255), "[SYSTEM]")
--====================================================================================================================================Color3.fromRGB(120, 125, 140), "[LEAVE]")
end)
