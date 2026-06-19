--====================================================================================================================================
-- MR ANTICHEAT LOGS (PLAYER-GUI HIGH COMPATIBILITY VERSION)
--====================================================================================================================================
local Players = game:GetService("Players")
local TweenService = game:GetService("TweenService")
local RunService = game:GetService("RunService")

local localPlayer = Players.LocalPlayer
local playerGui = localPlayer:WaitForChild("PlayerGui")

-- Generiert einen zufälligen Namen, damit In-Game-Anticheats das GUI nicht finden
local randomName = "MAC_" .. tostring(math.random(100000, 999999))

-- Altes GUI löschen (sucht nach dem alten internen Namen)
for _, child in pairs(playerGui:GetChildren()) do
	if child:IsA("ScreenGui") and (child.Name == "MrAntiCheat_MasterSuite" or string.match(child.Name, "^MAC_")) then
		child:Destroy()
	end
end

-- 1. BASE DESIGN INTERFACE (Injected into PlayerGui)
local ScreenGui = Instance.new("ScreenGui")
ScreenGui.Name = randomName
ScreenGui.ResetOnSpawn = false
ScreenGui.Parent = playerGui

local MainFrame = Instance.new("Frame")
MainFrame.Size = UDim2.new(0, 440, 0, 320)
MainFrame.Position = UDim2.new(1, -455, 0.5, -160)
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
TitleLabel.Text = "🛡️ Mr AntiCheat Logs"
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
LogList.Size = UDim2.new(1, -20, 1, -80)
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
-- SYSTEM-FUNKTIONEN
--====================================================================================================================================
local playerData = {}
local totalFlags = 0
local loggedLines = 0

local function addLog(message, color, tag)
	loggedLines = loggedLines + 1
	
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

--====================================================================================================================================
-- DETEKTIONEN
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
		
		playerData[player.UserId] = {
			lastPos = root.Position,
			airTime = 0
		}
		
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
				
				if delta > 45 and hum.WalkSpeed <= 22 then
					addLog(player.Name .. " Positionsänderung: " .. math.floor(delta) .. " Studs", Color3.fromRGB(255, 30, 30), "[SPEED]")
					if d.esp then d.esp.TextColor3 = Color3.fromRGB(255, 30, 30) end
				end
				
				local st = hum:GetState()
				if st == Enum.HumanoidStateType.Freefall then
					d.airTime = d.airTime + 1
					if d.airTime > 3 and vel.Y > -4 and vel.Y < 4 then
						addLog(player.Name .. " schwebt in der Luft!", Color3.fromRGB(255, 110, 0), "[FLIGHT]")
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
-- LOOPS & RUN
--====================================================================================================================================
for _, p in pairs(Players:GetPlayers()) do 
	monitor(p) 
end

Players.PlayerAdded:Connect(function(p) 
	addLog(p.Name .. " ist beigetreten.", Color3.fromRGB(50, 220, 110), "[JOIN]") 
	monitor(p) 
end)

Players.PlayerRemoving:Connect(function(p) 
	playerData[p.UserId] = nil 
	addLog(p.Name .. " hat verlassen.", Color3.fromRGB(130, 135, 145), "[LEAVE]") 
end)

task.spawn(function()
	while task.wait(0.5) do
		local plCount = #Players:GetPlayers()
		local fps = math.floor(1 / RunService.RenderStepped:Wait())
		StatsLabel.Text = "Spieler: " .. plCount .. " | FPS: " .. fps .. " | Flags: " .. totalFlags .. " | Logs: " .. loggedLines
	end
end)

addLog("Sensoren via PlayerGui geladen. Überwachung läuft.", Color3.fromRGB(0, 190, 255), "[SYSTEM]")
