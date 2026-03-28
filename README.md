local player = game.Players.LocalPlayer
local character = player.Character or player.CharacterAdded:Wait()
local humanoid = character:WaitForChild("Humanoid")
local rootPart = character:WaitForChild("HumanoidRootPart")

local PlayerModule = require(player:WaitForChild("PlayerScripts"):WaitForChild("PlayerModule"))
local Controls = PlayerModule:GetControls()

local waypoints = {}
local isRunning = false
local currentIndex = 1

local RunService = game:GetService("RunService")
local color1 = Color3.fromRGB(180, 100, 255) 
local color2 = Color3.fromRGB(40, 10, 80)  
local currentGradiantColor = color1

task.spawn(function()
    local t = 0
    RunService.Heartbeat:Connect(function(dt)
        t = t + dt * 0.6
        local ratio = (math.sin(t) + 1) / 2
        currentGradiantColor = color1:Lerp(color2, ratio)
    end)
end)

local ScreenGui = Instance.new("ScreenGui")
ScreenGui.Name = "ClearTextWaypointGui"
ScreenGui.ResetOnSpawn = false
ScreenGui.Parent = player:WaitForChild("PlayerGui")

local function applyGradiant(obj, prop)
    RunService.Heartbeat:Connect(function()
        if obj and obj.Parent then
            obj[prop] = currentGradiantColor
        end
    end)
end

local btnHide = Instance.new("ImageButton", ScreenGui)
btnHide.Size = UDim2.new(0, 45, 0, 45)
btnHide.Position = UDim2.new(0, 10, 0.5, -135)
btnHide.BackgroundColor3 = Color3.fromRGB(20, 20, 25)
btnHide.Image = "rbxassetid://100142668268808"
Instance.new("UICorner", btnHide).CornerRadius = UDim.new(0, 12)
local hStroke = Instance.new("UIStroke", btnHide)
hStroke.Thickness = 2
applyGradiant(hStroke, "Color")

local MasterFrame = Instance.new("Frame", ScreenGui)
MasterFrame.Size = UDim2.new(0, 180, 0, 240)
MasterFrame.Position = UDim2.new(0, 70, 0.5, -135)
MasterFrame.BackgroundTransparency = 1
MasterFrame.Active = true
MasterFrame.Draggable = true

local MainMenu = Instance.new("ImageLabel", MasterFrame)
MainMenu.Size = UDim2.new(1, 0, 1, 0)
MainMenu.BackgroundTransparency = 1
MainMenu.Image = "rbxassetid://136216611932487"
MainMenu.ScaleType = Enum.ScaleType.Crop
Instance.new("UICorner", MainMenu).CornerRadius = UDim.new(0, 15)
local mStroke = Instance.new("UIStroke", MainMenu)
mStroke.Thickness = 3
applyGradiant(mStroke, "Color")

local listLayout = Instance.new("UIListLayout", MainMenu)
listLayout.Padding = UDim.new(0, 8)
listLayout.HorizontalAlignment = Enum.HorizontalAlignment.Center
listLayout.VerticalAlignment = Enum.VerticalAlignment.Center
Instance.new("UIPadding", MainMenu).PaddingTop = UDim.new(0, 10)

local function createStyledBtn(text, order)
    local btn = Instance.new("TextButton", MainMenu)
    btn.Size = UDim2.new(0.9, 0, 0, 38)
    btn.BackgroundColor3 = Color3.fromRGB(10, 10, 15)
    btn.BackgroundTransparency = 0.25 
    btn.Font = Enum.Font.GothamBold
    btn.TextSize = 13
    btn.TextColor3 = Color3.fromRGB(255, 255, 255)
    btn.Text = text
    btn.LayoutOrder = order
    btn.AutoButtonColor = true
    
    Instance.new("UICorner", btn).CornerRadius = UDim.new(0, 8)
    
    local stroke = Instance.new("UIStroke", btn)
    stroke.Thickness = 1.2
    stroke.Transparency = 0.5
    applyGradiant(stroke, "Color") 
    
    return btn
end

local btnAdd = createStyledBtn("✓ СОЗДАТЬ ТОЧКУ", 1)
local btnStartStop = createStyledBtn("▶︎ ЗАПУСТИТЬ ПУТЬ", 2)
local btnClear = createStyledBtn("× ОЧИСТИТЬ ВСЁ", 3)
local btnExit = createStyledBtn("🔌 ВЫКЛЮЧИТЬ", 4)

local btnTogglePanel = Instance.new("TextButton", MasterFrame)
btnTogglePanel.Size = UDim2.new(0, 22, 0, 80)
btnTogglePanel.Position = UDim2.new(1, 5, 0.5, -40)
btnTogglePanel.BackgroundColor3 = Color3.fromRGB(20, 20, 25)
btnTogglePanel.Text = "〉"
btnTogglePanel.TextColor3 = Color3.fromRGB(255, 255, 255)
btnTogglePanel.Font = Enum.Font.GothamBold
btnTogglePanel.TextSize = 18
Instance.new("UICorner", btnTogglePanel).CornerRadius = UDim.new(0, 6)
local pStroke = Instance.new("UIStroke", btnTogglePanel)
applyGradiant(pStroke, "Color")

local ListPanel = Instance.new("ScrollingFrame", MasterFrame)
ListPanel.Size = UDim2.new(0, 170, 1, 0)
ListPanel.Position = UDim2.new(1, 35, 0, 0)
ListPanel.BackgroundColor3 = Color3.fromRGB(15, 10, 20)
ListPanel.BackgroundTransparency = 0.2
ListPanel.Visible = false
ListPanel.AutomaticCanvasSize = Enum.AutomaticSize.Y
ListPanel.ScrollBarThickness = 3
Instance.new("UICorner", ListPanel).CornerRadius = UDim.new(0, 10)
local lStroke = Instance.new("UIStroke", ListPanel)
applyGradiant(lStroke, "Color")

local scrollLayout = Instance.new("UIListLayout", ListPanel)
scrollLayout.Padding = UDim.new(0, 5)
scrollLayout.HorizontalAlignment = Enum.HorizontalAlignment.Center
Instance.new("UIPadding", ListPanel).PaddingTop = UDim.new(0, 8)

local MicroMenu = Instance.new("Frame", ScreenGui)
MicroMenu.Size = UDim2.new(0, 200, 0, 130)
MicroMenu.Position = UDim2.new(0.5, -100, 0.5, -65)
MicroMenu.BackgroundColor3 = Color3.fromRGB(25, 20, 35)
MicroMenu.Visible = false
Instance.new("UICorner", MicroMenu).CornerRadius = UDim.new(0, 12)
local miStroke = Instance.new("UIStroke", MicroMenu)
miStroke.Thickness = 2
applyGradiant(miStroke, "Color")

local MicroTitle = Instance.new("TextLabel", MicroMenu)
MicroTitle.Size = UDim2.new(1, 0, 0, 35)
MicroTitle.BackgroundTransparency = 1
MicroTitle.Font = Enum.Font.GothamBold
MicroTitle.TextColor3 = Color3.fromRGB(255, 255, 255)
MicroTitle.TextSize = 14

local DelayInput = Instance.new("TextBox", MicroMenu)
DelayInput.Size = UDim2.new(0.8, 0, 0, 30)
DelayInput.Position = UDim2.new(0.1, 0, 0.35, 0)
DelayInput.BackgroundColor3 = Color3.fromRGB(10, 10, 15)
DelayInput.TextColor3 = Color3.fromRGB(255, 255, 255)
DelayInput.PlaceholderText = "Задержка (сек)"
Instance.new("UICorner", DelayInput).CornerRadius = UDim.new(0, 6)

local btnDeletePoint = Instance.new("TextButton", MicroMenu)
btnDeletePoint.Size = UDim2.new(0.4, 0, 0, 32)
btnDeletePoint.Position = UDim2.new(0.08, 0, 0.7, 0)
btnDeletePoint.BackgroundColor3 = Color3.fromRGB(180, 50, 50)
btnDeletePoint.Text = "× УДАЛИТЬ"
btnDeletePoint.TextColor3 = Color3.fromRGB(255, 255, 255)
btnDeletePoint.Font = Enum.Font.GothamBold
Instance.new("UICorner", btnDeletePoint).CornerRadius = UDim.new(0, 6)

local btnCloseMicro = Instance.new("TextButton", MicroMenu)
btnCloseMicro.Size = UDim2.new(0.4, 0, 0, 32)
btnCloseMicro.Position = UDim2.new(0.52, 0, 0.7, 0)
btnCloseMicro.BackgroundColor3 = Color3.fromRGB(60, 60, 70)
btnCloseMicro.Text = "✓ ГОТОВО"
btnCloseMicro.TextColor3 = Color3.fromRGB(255, 255, 255)
btnCloseMicro.Font = Enum.Font.GothamBold
Instance.new("UICorner", btnCloseMicro).CornerRadius = UDim.new(0, 6)

local selectedPointData = nil

btnHide.MouseButton1Click:Connect(function() MasterFrame.Visible = not MasterFrame.Visible end)
btnTogglePanel.MouseButton1Click:Connect(function()
    ListPanel.Visible = not ListPanel.Visible
    btnTogglePanel.Text = ListPanel.Visible and "〈" or "〉"
end)

local function updateVisuals()
    for i, data in ipairs(waypoints) do
        data.marker:FindFirstChild("BillboardGui").TextLabel.Text = tostring(i)
        data.listBtn.Text = "▫️◽ Точка " .. i .. " [" .. data.delay .. "s]"
        if data.beam then data.beam:Destroy() end
        data.beam = nil
        if i > 1 then
            local prevData = waypoints[i - 1]
            local beam = Instance.new("Beam", data.marker)
            beam.Attachment0 = prevData.marker:FindFirstChild("Attachment")
            beam.Attachment1 = data.marker:FindFirstChild("Attachment")
            beam.FaceCamera = true
            applyGradiant(beam, "Color")
            beam.Width0 = 0.5; beam.Width1 = 0.5
            beam.Transparency = NumberSequence.new(0.2)
            data.beam = beam
        end
    end
end

local function walkLoop()
    while isRunning and #waypoints > 0 do
        if currentIndex > #waypoints then currentIndex = 1 end
        local targetData = waypoints[currentIndex]
        if not targetData then break end
        local reached = false
        while isRunning and not reached do
            humanoid:MoveTo(targetData.pos)
            local success = humanoid.MoveToFinished:Wait()
            local distance = (rootPart.Position - targetData.pos).Magnitude
            if success or distance < 4 then reached = true end
        end
        if isRunning and targetData.delay > 0 then task.wait(targetData.delay) end
        if isRunning then currentIndex = currentIndex + 1 end
    end
end

btnAdd.MouseButton1Click:Connect(function()
    local pos = rootPart.Position
    local marker = Instance.new("Part", workspace)
    marker.Size = Vector3.new(2.5, 0.2, 2.5)
    marker.Position = pos - Vector3.new(0, 3, 0)
    marker.Anchored = true; marker.CanCollide = false
    marker.Material = Enum.Material.Neon; marker.Transparency = 0.4
    applyGradiant(marker, "Color")
    
    local att = Instance.new("Attachment", marker)
    local bg = Instance.new("BillboardGui", marker)
    bg.Size = UDim2.new(0, 40, 0, 40); bg.StudsOffset = Vector3.new(0, 2.5, 0); bg.AlwaysOnTop = true
    local label = Instance.new("TextLabel", bg)
    label.Size = UDim2.new(1,0,1,0); label.BackgroundTransparency = 1; label.TextScaled = true
    label.TextColor3 = Color3.fromRGB(255, 255, 255); label.Font = Enum.Font.GothamBlack

    local listBtn = Instance.new("TextButton", ListPanel)
    listBtn.Size = UDim2.new(0.95, 0, 0, 35); listBtn.BackgroundColor3 = Color3.fromRGB(30, 25, 40)
    listBtn.TextColor3 = Color3.fromRGB(255, 255, 255); listBtn.Font = Enum.Font.GothamSemibold
    listBtn.TextSize = 12; Instance.new("UICorner", listBtn).CornerRadius = UDim.new(0, 6)
    
    local pointData = { pos = pos, delay = 0, marker = marker, listBtn = listBtn, beam = nil }
    table.insert(waypoints, pointData)
    
    listBtn.MouseButton1Click:Connect(function()
        selectedPointData = pointData
        MicroTitle.Text = "НАСТРОЙКА Т-" .. table.find(waypoints, pointData)
        DelayInput.Text = tostring(pointData.delay)
        MicroMenu.Visible = true
    end)
    updateVisuals()
end)

btnCloseMicro.MouseButton1Click:Connect(function()
    if selectedPointData then
        selectedPointData.delay = tonumber(DelayInput.Text) or 0
        updateVisuals()
    end
    MicroMenu.Visible = false
end)

btnDeletePoint.MouseButton1Click:Connect(function()
    if selectedPointData then
        local idx = table.find(waypoints, selectedPointData)
        if idx then
            table.remove(waypoints, idx)
            selectedPointData.marker:Destroy(); selectedPointData.listBtn:Destroy()
            if currentIndex > idx then currentIndex = currentIndex - 1 end
            updateVisuals()
        end
    end
    MicroMenu.Visible = false
end)

btnStartStop.MouseButton1Click:Connect(function()
    if #waypoints == 0 then return end
    isRunning = not isRunning
    if isRunning then 
        btnStartStop.Text = "◀︎ ПАУЗА ПУТИ"; Controls:Disable(); task.spawn(walkLoop)
    else 
        btnStartStop.Text = "▶︎ ЗАПУСТИТЬ ПУТЬ"; Controls:Enable(); humanoid:MoveTo(rootPart.Position) 
    end
end)

btnClear.MouseButton1Click:Connect(function()
    isRunning = false; Controls:Enable()
    for _, d in ipairs(waypoints) do d.marker:Destroy(); d.listBtn:Destroy() end
    waypoints = {}; currentIndex = 1; updateVisuals()
end)

btnExit.MouseButton1Click:Connect(function()
    isRunning = false; Controls:Enable()
    for _, d in ipairs(waypoints) do d.marker:Destroy() end
    ScreenGui:Destroy(); script:Destroy()
end)
