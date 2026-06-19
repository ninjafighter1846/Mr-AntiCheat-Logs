-- DIESEN CODE IN DEN EXECUTOR KOPIEREN
local Players = game:GetService("Players")
local CoreGui = game:GetService("CoreGui")
local TweenService = game:GetService("TweenService")

-- Falls das GUI schon existiert, altes löschen
if CoreGui:FindFirstChild("MrAntiCheat_PremiumLogs") then
	CoreGui["MrAntiCheat_PremiumLogs"]:Destroy()
end

-- 1. PREMIUM GUI ERSTELLEN
local ScreenGui = Instance.new("ScreenGui")
ScreenGui.Name = "MrAntiCheat_PremiumLogs"
ScreenGui.ResetOnSpawn = false
ScreenGui.Parent = CoreGui

-- Hauptfenster (Edles Dark-Design)
local MainFrame = Instance.new("Frame")
MainFrame.Name = "MainFrame"
MainFrame.Size = UDim2.new(0, 380, 0, 260)
MainFrame.Position = UDim2.new(1, -395, 0.5, -130)
MainFrame.BackgroundColor3 = Color3.fromRGB(11, 12, 15)
MainFrame.BackgroundTransparency = 0.1
MainFrame.BorderSizePixel = 0
MainFrame.Active = true
MainFrame.Draggable = true
MainFrame.Parent = ScreenGui

-- Abgerundete Ecken
local MainCorner = Instance.new("UICorner")
MainCorner.CornerRadius = UDim.new(0, 10)
MainCorner.Parent = MainFrame

-- Neon-Rauputz / Stroke (Edler Rand)
local MainStroke = Instance.new("UIStroke")
MainStroke.Thickness = 1.5
MainStroke.Color = Color3.fromRGB(255, 35, 35) -- Hacker-Rot
MainStroke.ApplyStrokeMode = Enum.ApplyStrokeMode.Border
MainStroke.Parent = MainFrame

-- Titel-Leiste
local TitleFrame = Instance.new("Frame")
TitleFrame.Size = UDim2.new(1, 0, 0, 35)
TitleFrame.BackgroundColor3 = Color3.fromRGB(18, 19, 24)
TitleFrame.BorderSizePixel = 0
TitleFrame.Parent = MainFrame

local TitleCorner = Instance.new("UICorner")
TitleCorner.CornerRadius = UDim.new(0, 10)
TitleCorner.Parent = TitleFrame

local TitleLabel = Instance.new("TextLabel")
TitleLabel.Size = UDim2.new(1, -20, 1, 0)
TitleLabel.Position = UDim2.new(0, 15, 0, 0)
TitleLabel.BackgroundTransparency = 1
TitleLabel.Text = "🛡️ Mr. AntiCheat Logs"
TitleLabel.TextColor3 = Color3.fromRGB(255, 255, 255)
TitleLabel.Font = Enum.Font.GothamBold
TitleLabel.TextSize = 14
TitleLabel.TextXAlignment = Enum.TextXAlignment.Left
TitleLabel.Parent = TitleFrame

-- Trennlinie unter dem Titel
local Line = Instance.new("Frame")
Line.Size = UDim2.new(1, 0, 0, 1)
Line.Position = UDim2.new(0, 0, 1, -1)
Line.BackgroundColor3 = Color3.fromRGB(35, 38, 47)
Line.BorderSizePixel = 0
Line.Parent = TitleFrame

-- Scrolling Frame für die Logs
local LogList = Instance.new("ScrollingFrame")
LogList.Size = UDim2.new(1, -20, 1, -50)
LogList.Position = UDim2.new(0, 10, 0, 42)
LogList.BackgroundTransparency = 1
LogList.BorderSizePixel = 0
LogList.CanvasSize = UDim2.new(0, 0, 0, 0)
LogList.ScrollBarThickness = 3
LogList.ScrollBarImageColor3 = Color3.fromRGB(255, 35, 35)
LogList.Parent = MainFrame

local UIListLayout = Instance.new("UIListLayout")
UIListLayout.SortOrder = Enum.SortOrder.LayoutOrder
UIListLayout.Padding = UDim.new(0, 4)
UIListLayout.Parent = LogList

-- 2. FUNKTION FÜR SCHÖNE LOGS (Mit Fade-In Animation)
local function addLog(text, color, prefix)
	local logRow = Instance.new("Frame")
	logRow.Size = UDim2.new(1, -5, 0, 22)
	logRow.BackgroundColor3 = Color3.fromRGB(20, 21, 26)
	logRow.BackgroundTransparency = 0.4
	logRow.BorderSizePixel = 0
	logRow.Parent = LogList
	
	local rowCorner = Instance.new("UICorner")
	rowCorner.CornerRadius = UDim.new(0, 4)
	rowCorner.Parent = logRow
	
	-- Zeitstempel
	local timeStr = os.date("%X")
	local timeLabel = Instance.new("TextLabel")
	timeLabel.Size = UDim2.new(0, 55, 1, 0)
	timeLabel.Position = UDim2.new(0, 6, 0, 0)
	timeLabel.BackgroundTransparency = 1
	timeLabel.Text = "[" .. timeStr .. "]"
	timeLabel.TextColor3 = Color3.fromRGB(100, 105, 120)
	timeLabel.Font = Enum.Font.Code
	timeLabel.TextSize = 11
	timeLabel.TextXAlignment = Enum.TextXAlignment.Left
	timeLabel.Parent = logRow

	-- Tag Prefix (z.B. [SPEED])
	local tagLabel = Instance.new("TextLabel")
	tagLabel.Size = UDim2.new(0, 65, 1, 0)
	tagLabel.Position = UDim2.new(0, 65, 0, 0)
	tagLabel.BackgroundTransparency = 1
	tagLabel.Text = prefix or "[INFO]"
	tagLabel.TextColor3 = color
	tagLabel.Font = Enum.Font.GothamBold
	tagLabel.TextSize = 10
	tagLabel.TextXAlignment = Enum.TextXAlignment.Left
	tagLabel.Parent = logRow

	-- Der eigentliche Log-Text
	local textLabel = Instance.new("TextLabel")
	textLabel.Size = UDim2.new(1, -135, 1, 0)
	textLabel.Position = UDim2.new(0, 130, 0, 0)
	textLabel.BackgroundTransparency = 1
	textLabel.Text = text
	textLabel.TextColor3 = Color3.fromRGB(230, 235, 245)
	textLabel.Font = Enum.Font.Gotham
	textLabel.TextSize = 11
	textLabel.TextXAlignment = Enum.TextXAlignment.Left
	textLabel.Parent = logRow
	
	-- Einblend-Animation (Fade-In)
	logRow.BackgroundTransparency = 1
	timeLabel.TextTransparency = 1
	tagLabel.TextTransparency = 1
	textLabel.TextTransparency = 1
	
	TweenService:Create(logRow, TweenInfo.new(0.3), {BackgroundTransparency = 0.4}):Play()
	TweenService:Create(timeLabel, TweenInfo.new(0.3), {TextTransparency = 0}):Play()
	TweenService:Create(tagLabel, TweenInfo.new(0.3), {TextTransparency = 0}):Play()
	TweenService:Create(textLabel, TweenInfo.new(0.3), {TextTransparency = 0}):Play()
	
	-- Scroll-Anpassung
	LogList.CanvasSize = UDim2.new(0, 0, 0, UIListLayout.AbsoluteContentSize.Y + 10)
	LogList.CanvasPosition = Vector2.new(0, LogList.AbsoluteCanvasSize.Y)
end

addLog("System erfolgreich injiziert.", Color3.fromRGB(0, 170, 255), "[SYSTEM]")

-- 3. ZUSTANDS-SCANNER FÜR SPIELER
local playerData = {}

local function monitorPlayer(player)
	if player == Players.LocalPlayer then return end
	
	local function onCharAdded(char)
		local root = char:WaitForChild("HumanoidRootPart", 10)
		local hum = char:WaitForChild("Humanoid", 10)
		if not root or not hum then return end
		
		playerData[player.UserId] = {
			lastPos = root.Position,
			airTime = 0
		}
		
		task.spawn(function()
			while char and char.Parent and task.wait(1) do
				if root and hum and hum.Health > 0 then
					local data = playerData[player.UserId]
					if not data then break end
					
					local currentPos = root.Position
					local distance = (currentPos - data.lastPos).Magnitude
					local velocity = root.AssemblyLinearVelocity
					
					-- 1. SPEED / TELEPORT CHECK
					if distance > 45 and hum.WalkSpeed <= 20 then
						addLog(player.Name .. " (" .. math.floor(distance) .. " Studs/s)", Color3.fromRGB(255, 35, 35), "[SPEED]")
					end
					
					-- 2. FLY CHECK
					local state = hum:GetState()
					if state == Enum.HumanoidStateType.Freefall then
						data.airTime = data.airTime + 1
						if data.airTime > 4 and velocity.Y > -5 then
							addLog(player.Name .. " schwebt in der Luft", Color3.fromRGB(255, 140, 0), "[FLY]")
						end
					else
						data.airTime = 0
					end
					
					-- 3. HIGH JUMP CHECK
					if velocity.Y > 65 and hum.JumpPower <= 55 then
						addLog(player.Name .. " ist extrem hoch gesprungen", Color3.fromRGB(180, 70, 255), "[JUMP]")
					end
					
					data.lastPos = currentPos
				end
			end
		end)
	end

	if player.Character then onCharAdded(player.Character) end
	player.CharacterAdded:Connect(onCharAdded)
end

-- Tracker starten
for _, p in pairs(Players:GetPlayers()) do monitorPlayer(p) end
Players.PlayerAdded:Connect(monitorPlayer)

-- Join / Leave Log-Designs
Players.PlayerAdded:Connect(function(p)
	addLog(p.Name .. " ist dem Server beigetreten.", Color3.fromRGB(40, 200, 100), "[JOIN]")
end)

Players.PlayerRemoving:Connect(function(p)
	playerData[p.UserId] = nil
	addLog(p.Name .. " hat das Spiel verlassen.", Color3.fromRGB(120, 125, 140), "[LEAVE]")
end)
