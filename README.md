-- ============================================================
-- MENU HỢP NHẤT: AIMBOT + ESP + MISC (GOD, FLY, SPEED, JUMP, NOCLIP)
-- THÊM NHIỀU CHỨC NĂNG MỚI CHO MISC
-- ============================================================

local Players = game:GetService("Players")
local RunService = game:GetService("RunService")
local Camera = workspace.CurrentCamera
local UserInputService = game:GetService("UserInputService")
local LocalPlayer = Players.LocalPlayer

if not LocalPlayer then return end

-- ============================================================
-- BIẾN TOÀN CỤC
-- ============================================================
local FOV = 180
local aimbotEnabled = false
local espEnabled = false
local godModeEnabled = false
local flyEnabled = false
local speedEnabled = false
local jumpEnabled = false
local noclipEnabled = false
local infiniteStamina = false

local isDragging = false
local dragStart, btnStartPos
local humanoid = nil
local rootPart = nil
local flyVelocity = Vector3.new(0, 20, 0)
local espDrawings = {}
local sliderDragging = false
local menuVisible = true
local speedMultiplier = 2
local jumpPower = 100
local noclipParts = {}

-- ============================================================
-- TẠO GUI
-- ============================================================
local gui = Instance.new("ScreenGui")
gui.Name = "SuperMenu"
gui.ResetOnSpawn = false
gui.Parent = LocalPlayer:WaitForChild("PlayerGui") or game.CoreGui

-- ============================================================
-- NÚT HÌNH TRÒN MÀU ĐỎ (GÓC DƯỚI PHẢI)
-- ============================================================
local circleBtn = Instance.new("TextButton")
circleBtn.Size = UDim2.new(0, 60, 0, 60)
circleBtn.Position = UDim2.new(1, -80, 1, -80)
circleBtn.AnchorPoint = Vector2.new(0.5, 0.5)
circleBtn.BackgroundColor3 = Color3.new(1, 0, 0)
circleBtn.BorderSizePixel = 0
circleBtn.Text = ""
circleBtn.Visible = false
circleBtn.ZIndex = 20
circleBtn.Parent = gui

local circleCorner = Instance.new("UICorner")
circleCorner.CornerRadius = UDim.new(1, 0)
circleCorner.Parent = circleBtn

circleBtn.MouseEnter:Connect(function() circleBtn.BackgroundColor3 = Color3.new(1, 0.2, 0.2) end)
circleBtn.MouseLeave:Connect(function() circleBtn.BackgroundColor3 = Color3.new(1, 0, 0) end)
circleBtn.MouseButton1Click:Connect(function()
    menuVisible = true
    mainFrame.Visible = true
    circleBtn.Visible = false
    if aimbotEnabled then fovFrame.Visible = true end
end)

-- ============================================================
-- MAIN FRAME
-- ============================================================
local mainFrame = Instance.new("Frame")
mainFrame.Size = UDim2.new(0, 380, 0, 500)
mainFrame.Position = UDim2.new(0.5, -190, 0.5, -250)
mainFrame.BackgroundColor3 = Color3.new(0.15, 0.15, 0.15)
mainFrame.BackgroundTransparency = 0.1
mainFrame.BorderSizePixel = 0
mainFrame.ZIndex = 10
mainFrame.Parent = gui

local cornerMain = Instance.new("UICorner")
cornerMain.CornerRadius = UDim.new(0, 8)
cornerMain.Parent = mainFrame

local title = Instance.new("TextLabel")
title.Size = UDim2.new(1, 0, 0, 40)
title.Position = UDim2.new(0, 0, 0, 0)
title.BackgroundTransparency = 1
title.Text = "   SUPER MENU"
title.TextColor3 = Color3.new(1, 1, 1)
title.TextSize = 22
title.TextXAlignment = Enum.TextXAlignment.Left
title.Font = Enum.Font.GothamBold
title.ZIndex = 10
title.Parent = mainFrame

local closeBtn = Instance.new("TextButton")
closeBtn.Size = UDim2.new(0, 30, 0, 30)
closeBtn.Position = UDim2.new(1, -35, 0, 5)
closeBtn.BackgroundTransparency = 1
closeBtn.Text = "X"
closeBtn.TextColor3 = Color3.new(1, 0, 0)
closeBtn.TextSize = 18
closeBtn.Font = Enum.Font.GothamBold
closeBtn.ZIndex = 10
closeBtn.Parent = mainFrame
closeBtn.MouseButton1Click:Connect(function()
    menuVisible = false
    mainFrame.Visible = false
    circleBtn.Visible = true
    fovFrame.Visible = false
end)

local dragBar = Instance.new("Frame")
dragBar.Size = UDim2.new(1, 0, 0, 40)
dragBar.Position = UDim2.new(0, 0, 0, 0)
dragBar.BackgroundTransparency = 1
dragBar.ZIndex = 10
dragBar.Parent = mainFrame
dragBar.InputBegan:Connect(function(input)
    if input.UserInputType == Enum.UserInputType.MouseButton1 then
        isDragging = true
        dragStart = input.Position
        btnStartPos = mainFrame.Position
    end
end)
dragBar.InputChanged:Connect(function(input)
    if input.UserInputType == Enum.UserInputType.MouseMovement and isDragging then
        local delta = Vector2.new(input.Position.X - dragStart.X, input.Position.Y - dragStart.Y)
        mainFrame.Position = UDim2.new(
            btnStartPos.X.Scale,
            btnStartPos.X.Offset + delta.X,
            btnStartPos.Y.Scale,
            btnStartPos.Y.Offset + delta.Y
        )
    end
end)
dragBar.InputEnded:Connect(function(input)
    if input.UserInputType == Enum.UserInputType.MouseButton1 then
        isDragging = false
    end
end)

-- ============================================================
-- TAB CONTAINER
-- ============================================================
local tabContainer = Instance.new("Frame")
tabContainer.Size = UDim2.new(1, 0, 0, 40)
tabContainer.Position = UDim2.new(0, 0, 0, 40)
tabContainer.BackgroundTransparency = 1
tabContainer.ZIndex = 10
tabContainer.Parent = mainFrame

local tabAim = Instance.new("TextButton")
tabAim.Size = UDim2.new(0.333, 0, 1, 0)
tabAim.Position = UDim2.new(0, 0, 0, 0)
tabAim.BackgroundColor3 = Color3.new(0.3, 0.3, 0.3)
tabAim.Text = "Aim"
tabAim.TextColor3 = Color3.new(1,1,1)
tabAim.ZIndex = 10
tabAim.Parent = tabContainer

local tabESP = Instance.new("TextButton")
tabESP.Size = UDim2.new(0.333, 0, 1, 0)
tabESP.Position = UDim2.new(0.333, 0, 0, 0)
tabESP.BackgroundColor3 = Color3.new(0.2, 0.2, 0.2)
tabESP.Text = "ESP"
tabESP.TextColor3 = Color3.new(1,1,1)
tabESP.ZIndex = 10
tabESP.Parent = tabContainer

local tabMisc = Instance.new("TextButton")
tabMisc.Size = UDim2.new(0.333, 0, 1, 0)
tabMisc.Position = UDim2.new(0.666, 0, 0, 0)
tabMisc.BackgroundColor3 = Color3.new(0.2, 0.2, 0.2)
tabMisc.Text = "Misc"
tabMisc.TextColor3 = Color3.new(1,1,1)
tabMisc.ZIndex = 10
tabMisc.Parent = tabContainer

local contentFrame = Instance.new("Frame")
contentFrame.Size = UDim2.new(1, -20, 1, -100)
contentFrame.Position = UDim2.new(0, 10, 0, 85)
contentFrame.BackgroundTransparency = 1
contentFrame.ZIndex = 10
contentFrame.Parent = mainFrame

-- ============================================================
-- TAB AIM
-- ============================================================
local aimTab = Instance.new("Frame")
aimTab.Size = UDim2.new(1, 0, 1, 0)
aimTab.BackgroundTransparency = 1
aimTab.Visible = true
aimTab.ZIndex = 10
aimTab.Parent = contentFrame

local aimBtn = Instance.new("TextButton")
aimBtn.Size = UDim2.new(0, 120, 0, 40)
aimBtn.Position = UDim2.new(0, 10, 0, 10)
aimBtn.BackgroundColor3 = Color3.new(0.2, 0.2, 0.2)
aimBtn.TextColor3 = Color3.new(1,1,1)
aimBtn.Text = "Aimbot: OFF"
aimBtn.ZIndex = 10
aimBtn.Parent = aimTab
aimBtn.MouseButton1Click:Connect(function()
    aimbotEnabled = not aimbotEnabled
    aimBtn.Text = aimbotEnabled and "Aimbot: ON" or "Aimbot: OFF"
    aimBtn.BackgroundColor3 = aimbotEnabled and Color3.new(0, 0.8, 0) or Color3.new(0.2, 0.2, 0.2)
    if aimbotEnabled and menuVisible then fovFrame.Visible = true else fovFrame.Visible = false end
end)

local fovLabel = Instance.new("TextLabel")
fovLabel.Size = UDim2.new(0, 80, 0, 30)
fovLabel.Position = UDim2.new(0, 10, 0, 60)
fovLabel.BackgroundTransparency = 1
fovLabel.Text = "FOV: 180"
fovLabel.TextColor3 = Color3.new(1,1,1)
fovLabel.TextSize = 14
fovLabel.ZIndex = 10
fovLabel.Parent = aimTab

local fovSlider = Instance.new("Frame")
fovSlider.Size = UDim2.new(0, 150, 0, 10)
fovSlider.Position = UDim2.new(0, 100, 0, 68)
fovSlider.BackgroundColor3 = Color3.new(0.3, 0.3, 0.3)
fovSlider.ZIndex = 10
fovSlider.Parent = aimTab

local fovFill = Instance.new("Frame")
fovFill.Size = UDim2.new(1, 0, 1, 0)
fovFill.BackgroundColor3 = Color3.new(0, 0.6, 1)
fovFill.ZIndex = 10
fovFill.Parent = fovSlider

local fovBtn = Instance.new("TextButton")
fovBtn.Size = UDim2.new(0, 16, 0, 16)
fovBtn.Position = UDim2.new(1, -8, 0.5, -8)
fovBtn.BackgroundColor3 = Color3.new(1,1,1)
fovBtn.Text = ""
fovBtn.ZIndex = 10
fovBtn.Parent = fovSlider

fovBtn.InputBegan:Connect(function(input)
    if input.UserInputType == Enum.UserInputType.MouseButton1 then sliderDragging = true end
end)
fovBtn.InputEnded:Connect(function(input)
    if input.UserInputType == Enum.UserInputType.MouseButton1 then sliderDragging = false end
end)

local function updateFOV()
    local pos = fovBtn.Position.X.Offset
    local percent = (pos + 8) / (fovSlider.Size.X.Offset)
    FOV = math.floor(percent * 300 + 20)
    FOV = math.clamp(FOV, 20, 320)
    fovLabel.Text = "FOV: " .. tostring(FOV)
    fovFill.Size = UDim2.new(percent, 0, 1, 0)
    fovFrame.Size = UDim2.new(0, FOV*2, 0, FOV*2)
end

UserInputService.InputChanged:Connect(function(input)
    if input.UserInputType == Enum.UserInputType.MouseMovement and sliderDragging then
        local relativeX = input.Position.X - fovSlider.AbsolutePosition.X
        local newX = math.clamp(relativeX - 8, 0, fovSlider.Size.X.Offset - 16)
        fovBtn.Position = UDim2.new(0, newX, 0.5, -8)
        updateFOV()
    end
end)

local fovFrame = Instance.new("Frame")
fovFrame.Size = UDim2.new(0, FOV*2, 0, FOV*2)
fovFrame.AnchorPoint = Vector2.new(0.5, 0.5)
fovFrame.Position = UDim2.new(0.5, 0, 0.5, 0)
fovFrame.BackgroundTransparency = 1
fovFrame.Visible = false
fovFrame.ZIndex = 0
fovFrame.Parent = gui

local fovCorner = Instance.new("UICorner")
fovCorner.CornerRadius = UDim.new(1, 0)
fovCorner.Parent = fovFrame

local fovStroke = Instance.new("UIStroke")
fovStroke.Color = Color3.new(1, 0.5, 0)
fovStroke.Thickness = 2
fovStroke.Parent = fovFrame

-- ============================================================
-- TAB ESP
-- ============================================================
local espTab = Instance.new("Frame")
espTab.Size = UDim2.new(1, 0, 1, 0)
espTab.BackgroundTransparency = 1
espTab.Visible = false
espTab.ZIndex = 10
espTab.Parent = contentFrame

local espBtn = Instance.new("TextButton")
espBtn.Size = UDim2.new(0, 120, 0, 40)
espBtn.Position = UDim2.new(0, 10, 0, 10)
espBtn.BackgroundColor3 = Color3.new(0.2, 0.2, 0.2)
espBtn.TextColor3 = Color3.new(1,1,1)
espBtn.Text = "ESP: OFF"
espBtn.ZIndex = 10
espBtn.Parent = espTab
espBtn.MouseButton1Click:Connect(function()
    espEnabled = not espEnabled
    espBtn.Text = espEnabled and "ESP: ON" or "ESP: OFF"
    espBtn.BackgroundColor3 = espEnabled and Color3.new(0, 0.8, 0) or Color3.new(0.2, 0.2, 0.2)
    if not espEnabled then
        for _, v in pairs(espDrawings) do
            if v then
                for _, d in pairs(v) do
                    if d and d.Remove then d:Remove() end
                end
            end
        end
        espDrawings = {}
    end
end)

-- ============================================================
-- TAB MISC (THÊM NHIỀU CHỨC NĂNG)
-- ============================================================
local miscTab = Instance.new("Frame")
miscTab.Size = UDim2.new(1, 0, 1, 0)
miscTab.BackgroundTransparency = 1
miscTab.Visible = false
miscTab.ZIndex = 10
miscTab.Parent = contentFrame

-- God Mode
local godBtn = Instance.new("TextButton")
godBtn.Size = UDim2.new(0, 120, 0, 35)
godBtn.Position = UDim2.new(0, 10, 0, 10)
godBtn.BackgroundColor3 = Color3.new(0.2, 0.2, 0.2)
godBtn.TextColor3 = Color3.new(1,1,1)
godBtn.Text = "God: OFF"
godBtn.ZIndex = 10
godBtn.Parent = miscTab
godBtn.MouseButton1Click:Connect(function()
    godModeEnabled = not godModeEnabled
    godBtn.Text = godModeEnabled and "God: ON" or "God: OFF"
    godBtn.BackgroundColor3 = godModeEnabled and Color3.new(0, 0.8, 0) or Color3.new(0.2, 0.2, 0.2)
    if godModeEnabled and humanoid then humanoid.Health = humanoid.MaxHealth end
end)

-- Fly
local flyBtn = Instance.new("TextButton")
flyBtn.Size = UDim2.new(0, 120, 0, 35)
flyBtn.Position = UDim2.new(0, 140, 0, 10)
flyBtn.BackgroundColor3 = Color3.new(0.2, 0.2, 0.2)
flyBtn.TextColor3 = Color3.new(1,1,1)
flyBtn.Text = "Fly: OFF"
flyBtn.ZIndex = 10
flyBtn.Parent = miscTab
flyBtn.MouseButton1Click:Connect(function()
    flyEnabled = not flyEnabled
    flyBtn.Text = flyEnabled and "Fly: ON" or "Fly: OFF"
    flyBtn.BackgroundColor3 = flyEnabled and Color3.new(0, 0.8, 0) or Color3.new(0.2, 0.2, 0.2)
    if humanoid then humanoid.PlatformStand = flyEnabled end
end)

-- Speed Boost
local speedBtn = Instance.new("TextButton")
speedBtn.Size = UDim2.new(0, 120, 0, 35)
speedBtn.Position = UDim2.new(0, 10, 0, 55)
speedBtn.BackgroundColor3 = Color3.new(0.2, 0.2, 0.2)
speedBtn.TextColor3 = Color3.new(1,1,1)
speedBtn.Text = "Speed: OFF"
speedBtn.ZIndex = 10
speedBtn.Parent = miscTab
speedBtn.MouseButton1Click:Connect(function()
    speedEnabled = not speedEnabled
    speedBtn.Text = speedEnabled and "Speed: ON" or "Speed: OFF"
    speedBtn.BackgroundColor3 = speedEnabled and Color3.new(0, 0.8, 0) or Color3.new(0.2, 0.2, 0.2)
    if humanoid then
        if speedEnabled then
            humanoid.WalkSpeed = 16 * speedMultiplier
        else
            humanoid.WalkSpeed = 16
        end
    end
end)

-- Jump Boost
local jumpBtn = Instance.new("TextButton")
jumpBtn.Size = UDim2.new(0, 120, 0, 35)
jumpBtn.Position = UDim2.new(0, 140, 0, 55)
jumpBtn.BackgroundColor3 = Color3.new(0.2, 0.2, 0.2)
jumpBtn.TextColor3 = Color3.new(1,1,1)
jumpBtn.Text = "Jump: OFF"
jumpBtn.ZIndex = 10
jumpBtn.Parent = miscTab
jumpBtn.MouseButton1Click:Connect(function()
    jumpEnabled = not jumpEnabled
    jumpBtn.Text = jumpEnabled and "Jump: ON" or "Jump: OFF"
    jumpBtn.BackgroundColor3 = jumpEnabled and Color3.new(0, 0.8, 0) or Color3.new(0.2, 0.2, 0.2)
    if humanoid then
        if jumpEnabled then
            humanoid.JumpPower = jumpPower
        else
            humanoid.JumpPower = 50 -- giá trị mặc định thường là 50
        end
    end
end)

-- NoClip
local noclipBtn = Instance.new("TextButton")
noclipBtn.Size = UDim2.new(0, 120, 0, 35)
noclipBtn.Position = UDim2.new(0, 10, 0, 100)
noclipBtn.BackgroundColor3 = Color3.new(0.2, 0.2, 0.2)
noclipBtn.TextColor3 = Color3.new(1,1,1)
noclipBtn.Text = "NoClip: OFF"
noclipBtn.ZIndex = 10
noclipBtn.Parent = miscTab
noclipBtn.MouseButton1Click:Connect(function()
    noclipEnabled = not noclipEnabled
    noclipBtn.Text = noclipEnabled and "NoClip: ON" or "NoClip: OFF"
    noclipBtn.BackgroundColor3 = noclipEnabled and Color3.new(0, 0.8, 0) or Color3.new(0.2, 0.2, 0.2)
    if noclipEnabled then
        if rootPart then
            rootPart.CanCollide = false
        end
        for _, part in pairs(LocalPlayer.Character:GetDescendants()) do
            if part:IsA("BasePart") then
                part.CanCollide = false
                table.insert(noclipParts, part)
            end
        end
    else
        if rootPart then
            rootPart.CanCollide = true
        end
        for _, part in pairs(noclipParts) do
            if part and part.Parent then
                part.CanCollide = true
            end
        end
        noclipParts = {}
    end
end)

-- Infinite Stamina (nếu game có stamina, thường là attribute)
local staminaBtn = Instance.new("TextButton")
staminaBtn.Size = UDim2.new(0, 120, 0, 35)
staminaBtn.Position = UDim2.new(0, 140, 0, 100)
staminaBtn.BackgroundColor3 = Color3.new(0.2, 0.2, 0.2)
staminaBtn.TextColor3 = Color3.new(1,1,1)
staminaBtn.Text = "Stamina: OFF"
staminaBtn.ZIndex = 10
staminaBtn.Parent = miscTab
staminaBtn.MouseButton1Click:Connect(function()
    infiniteStamina = not infiniteStamina
    staminaBtn.Text = infiniteStamina and "Stamina: ON" or "Stamina: OFF"
    staminaBtn.BackgroundColor3 = infiniteStamina and Color3.new(0, 0.8, 0) or Color3.new(0.2, 0.2, 0.2)
    -- Nếu có attribute stamina, set nó lên max mỗi frame
end)

-- Thêm label hướng dẫn
local infoLabel = Instance.new("TextLabel")
infoLabel.Size = UDim2.new(1, 0, 0, 40)
infoLabel.Position = UDim2.new(0, 0, 0, 150)
infoLabel.BackgroundTransparency = 1
infoLabel.Text = "Speed x2 | Jump x2 | NoClip xuyen tuong"
infoLabel.TextColor3 = Color3.new(0.7, 0.7, 0.7)
infoLabel.TextSize = 12
infoLabel.ZIndex = 10
infoLabel.Parent = miscTab

-- ============================================================
-- CHUYỂN TAB
-- ============================================================
local function setTab(activeTab)
    aimTab.Visible = false
    espTab.Visible = false
    miscTab.Visible = false
    if activeTab == "Aim" then aimTab.Visible = true
    elseif activeTab == "ESP" then espTab.Visible = true
    elseif activeTab == "Misc" then miscTab.Visible = true
    end
    tabAim.BackgroundColor3 = (activeTab == "Aim") and Color3.new(0.3, 0.3, 0.3) or Color3.new(0.2, 0.2, 0.2)
    tabESP.BackgroundColor3 = (activeTab == "ESP") and Color3.new(0.3, 0.3, 0.3) or Color3.new(0.2, 0.2, 0.2)
    tabMisc.BackgroundColor3 = (activeTab == "Misc") and Color3.new(0.3, 0.3, 0.3) or Color3.new(0.2, 0.2, 0.2)
end

tabAim.MouseButton1Click:Connect(function() setTab("Aim") end)
tabESP.MouseButton1Click:Connect(function() setTab("ESP") end)
tabMisc.MouseButton1Click:Connect(function() setTab("Misc") end)
setTab("Aim")

-- ============================================================
-- PHÍM TẮT H
-- ============================================================
UserInputService.InputBegan:Connect(function(input, gameProcessed)
    if gameProcessed then return end
    if input.KeyCode == Enum.KeyCode.H then
        menuVisible = not menuVisible
        mainFrame.Visible = menuVisible
        circleBtn.Visible = not menuVisible
        if menuVisible and aimbotEnabled then fovFrame.Visible = true else fovFrame.Visible = false end
    end
end)

-- ============================================================
-- HÀM GOD MODE
-- ============================================================
local function setupGodMode(char)
    local hum = char:FindFirstChild("Humanoid")
    if hum then
        humanoid = hum
        hum.HealthChanged:Connect(function(newHealth)
            if godModeEnabled and hum and hum.Parent then
                if newHealth < hum.MaxHealth then hum.Health = hum.MaxHealth end
            end
        end)
        hum.Died:Connect(function()
            if godModeEnabled and hum then
                task.wait(0.1)
                if hum.Parent then hum.Health = hum.MaxHealth end
            end
        end)
        if godModeEnabled then hum.Health = hum.MaxHealth end
        -- Áp dụng speed/jump nếu đang bật
        if speedEnabled then hum.WalkSpeed = 16 * speedMultiplier end
        if jumpEnabled then hum.JumpPower = jumpPower end
    end
end

-- ============================================================
-- HÀM ESP
-- ============================================================
local function createESP(player)
    if espDrawings[player.UserId] then return espDrawings[player.UserId] end
    local drawings = {
        box = Drawing.new("Square"),
        line = Drawing.new("Line"),
        health = Drawing.new("Line"),
        boneHead = Drawing.new("Line"),
        boneTorso = Drawing.new("Line"),
        boneLArm = Drawing.new("Line"),
        boneRArm = Drawing.new("Line"),
        boneLLeg = Drawing.new("Line"),
        boneRLeg = Drawing.new("Line"),
    }
    for _, d in pairs(drawings) do
        d.Visible = false
        d.Thickness = 1
        d.Color = Color3.new(1, 1, 1)
    end
    drawings.box.Color = Color3.new(1, 0, 0)
    drawings.line.Color = Color3.new(0, 1, 0)
    drawings.health.Color = Color3.new(0, 1, 0)
    for k, d in pairs(drawings) do
        if k:find("bone") then
            d.Color = Color3.new(0.5, 0.5, 1)
            d.Thickness = 1.5
        end
    end
    espDrawings[player.UserId] = drawings
    return drawings
end

local function removeESP(playerId)
    if espDrawings[playerId] then
        for _, d in pairs(espDrawings[playerId]) do
            if d and d.Remove then d:Remove() end
       
