pcall(function() game.Players.LocalPlayer.Kick = function() end end)
local Player = game:GetService("Players").LocalPlayer
local UserInputService = game:GetService("UserInputService")
local RunService = game:GetService("RunService")
local Players = game:GetService("Players")
local Camera = workspace.CurrentCamera
local TweenService = game:GetService("TweenService")

-- =========================
-- CONFIGURAÇÕES INICIAIS
-- =========================
local isMobile = UserInputService.TouchEnabled
local scaleMultiplier = isMobile and 0.8 or 1
local buttonHeight = isMobile and 50 or 40
local fontSize = isMobile and 16 or 14
local notificationsEnabled = true

-- WHITELIST: Coloque aqui os UserIds dos jogadores que podem executar o script.
-- Ex: local allowedUserIds = { 12345678, 5011524982 }
local allowedUserIds = 5011524982, {
    -- coloque os IDs permitidos aqui
}

local function isAllowed()
    for _, id in ipairs(allowedUserIds) do
        if Player and Player.UserId == id then
            return true
        end
    end
    return false
end

if not isAllowed() then
    -- Caso não esteja na whitelist, não carrega o GUI
    pcall(function()
        Player:Kick("Você não tem permissão para executar este script.")
    end)
    return
end

-- =========================
-- GUI PRINCIPAL
-- =========================
local screenGui = Instance.new("ScreenGui")
screenGui.Name = "GankMenu"
screenGui.Parent = Player:WaitForChild("PlayerGui")
screenGui.ZIndexBehavior = Enum.ZIndexBehavior.Sibling
screenGui.ResetOnSpawn = false

local reopenButton = nil -- Botão de reabrir
local lastReopenButtonPosition = nil -- guarda a posição do reopenButton

local mainContainer = Instance.new("Frame")
mainContainer.Name = "MainContainer"
mainContainer.Parent = screenGui
mainContainer.BackgroundColor3 = Color3.fromRGB(255, 255, 255)
mainContainer.BackgroundTransparency = 0.05
mainContainer.Position = UDim2.new(0.5, 0, 0.5, 0)
mainContainer.Size = UDim2.new(0, 750 * scaleMultiplier, 0, 500 * scaleMultiplier)
mainContainer.AnchorPoint = Vector2.new(0.5, 0.5)
mainContainer.ClipsDescendants = true
Instance.new("UICorner", mainContainer).CornerRadius = UDim.new(0, 12)
local mainStroke = Instance.new("UIStroke")
mainStroke.Parent = mainContainer
mainStroke.Color = Color3.fromRGB(200, 40, 40)
mainStroke.Thickness = 2
mainStroke.Transparency = 0.7

local dragFrame = Instance.new("Frame")
dragFrame.Name = "DragFrame"
dragFrame.Parent = mainContainer
dragFrame.BackgroundTransparency = 1
dragFrame.Size = UDim2.new(1, 0, 0, 60)

local tabContainer = Instance.new("ScrollingFrame")
tabContainer.Name = "TabContainer"
tabContainer.Parent = mainContainer
tabContainer.BackgroundColor3 = Color3.fromRGB(240, 240, 245)
tabContainer.BackgroundTransparency = 0.1
tabContainer.Position = UDim2.new(0, 0, 0, 80)
tabContainer.Size = UDim2.new(0, 150 * scaleMultiplier, 0, 340 * scaleMultiplier)
tabContainer.CanvasSize = UDim2.new(0, 0, 0, 0)
tabContainer.ScrollBarThickness = 4
tabContainer.ScrollBarImageColor3 = Color3.fromRGB(200, 40, 40)
Instance.new("UICorner", tabContainer).CornerRadius = UDim.new(0, 8)
local tabLayout = Instance.new("UIListLayout")
tabLayout.Parent = tabContainer
tabLayout.SortOrder = Enum.SortOrder.LayoutOrder
tabLayout.Padding = UDim.new(0, 5)

local header = Instance.new("Frame")
header.Parent = mainContainer
header.BackgroundColor3 = Color3.fromRGB(250, 250, 255)
header.BackgroundTransparency = 0.1
header.Position = UDim2.new(0, 0, 0, 0)
header.Size = UDim2.new(0, 150 * scaleMultiplier, 0, 80 * scaleMultiplier)
Instance.new("UICorner", header).CornerRadius = UDim.new(0, 8)

local logo = Instance.new("ImageButton")
logo.Parent = header
logo.BackgroundTransparency = 1
logo.Position = UDim2.new(0.1, 0, 0.1, 0)
logo.Size = UDim2.new(0, 50 * scaleMultiplier, 0, 50 * scaleMultiplier)
logo.Image = "rbxassetid://117130789916072" -- novo ícone pedido
logo.ScaleType = Enum.ScaleType.Fit

local title = Instance.new("TextLabel")
title.Parent = header
title.BackgroundTransparency = 1
title.Position = UDim2.new(0, 0, 0.65, 0)
title.Size = UDim2.new(1, 0, 0, 25 * scaleMultiplier)
title.Font = Enum.Font.GothamBold
title.Text = "GANK"
title.TextColor3 = Color3.fromRGB(200, 40, 40)
title.TextSize = 16

local tabHeader = Instance.new("Frame")
tabHeader.Parent = mainContainer
tabHeader.BackgroundColor3 = Color3.fromRGB(250, 250, 255)
tabHeader.BackgroundTransparency = 0.1
tabHeader.Position = UDim2.new(0.2, 0, 0, 0)
tabHeader.Size = UDim2.new(0, 600 * scaleMultiplier, 0, 60 * scaleMultiplier)
Instance.new("UICorner", tabHeader).CornerRadius = UDim.new(0, 8)

local tabName = Instance.new("TextLabel")
tabName.Parent = tabHeader
tabName.BackgroundTransparency = 1
tabName.Position = UDim2.new(0.02, 0, 0.1, 0)
tabName.Size = UDim2.new(0, 150 * scaleMultiplier, 0, 40 * scaleMultiplier)
tabName.Font = Enum.Font.GothamBold
tabName.Text = "Inicio"
tabName.TextColor3 = Color3.fromRGB(200, 40, 40)
tabName.TextSize = 18
tabName.TextXAlignment = Enum.TextXAlignment.Left

local minimizeBtn = Instance.new("TextButton")
minimizeBtn.Parent = tabHeader
minimizeBtn.BackgroundColor3 = Color3.fromRGB(240, 240, 245)
minimizeBtn.Position = UDim2.new(0.78, 0, 0.15, 0)
minimizeBtn.Size = UDim2.new(0, 30 * scaleMultiplier, 0, 30 * scaleMultiplier)
minimizeBtn.Font = Enum.Font.GothamBold
minimizeBtn.Text = "-"
minimizeBtn.TextColor3 = Color3.fromRGB(0, 150, 0)
minimizeBtn.TextSize = 14
Instance.new("UICorner", minimizeBtn).CornerRadius = UDim.new(0, 6)

local closeBtn = Instance.new("TextButton")
closeBtn.Parent = tabHeader
closeBtn.BackgroundColor3 = Color3.fromRGB(240, 240, 245)
closeBtn.Position = UDim2.new(0.85, 0, 0.15, 0)
closeBtn.Size = UDim2.new(0, 30 * scaleMultiplier, 0, 30 * scaleMultiplier)
closeBtn.Font = Enum.Font.GothamBold
closeBtn.Text = "X"
closeBtn.TextColor3 = Color3.fromRGB(200, 50, 50)
closeBtn.TextSize = 14
Instance.new("UICorner", closeBtn).CornerRadius = UDim.new(0, 6)

local contentFrame = Instance.new("ScrollingFrame")
contentFrame.Parent = mainContainer
contentFrame.BackgroundColor3 = Color3.fromRGB(250, 250, 255)
contentFrame.BackgroundTransparency = 0.1
contentFrame.Position = UDim2.new(0.2, 0, 0.12, 0)
contentFrame.Size = UDim2.new(0, 600 * scaleMultiplier, 0, 370 * scaleMultiplier)
contentFrame.CanvasSize = UDim2.new(0, 0, 0, 0)
contentFrame.ScrollBarThickness = 4
contentFrame.ScrollBarImageColor3 = Color3.fromRGB(200, 40, 40)
Instance.new("UICorner", contentFrame).CornerRadius = UDim.new(0, 8)
local contentLayout = Instance.new("UIListLayout")
contentLayout.Parent = contentFrame
contentLayout.SortOrder = Enum.SortOrder.LayoutOrder
contentLayout.Padding = UDim.new(0, 8)

local notifContainer = Instance.new("Frame")
notifContainer.Name = "NotificationContainer"
notifContainer.Parent = screenGui
notifContainer.BackgroundTransparency = 1
notifContainer.Position = UDim2.new(1, -320, 0, 20)
notifContainer.Size = UDim2.new(0, 300, 1, -40)
notifContainer.ZIndex = 1000
local notifLayout = Instance.new("UIListLayout")
notifLayout.Parent = notifContainer
notifLayout.SortOrder = Enum.SortOrder.LayoutOrder
notifLayout.Padding = UDim.new(0, 10)
notifLayout.HorizontalAlignment = Enum.HorizontalAlignment.Right

local function notify(titleMsg, text, duration)
    if not notificationsEnabled then return end
    duration = duration or 3
    local notifFrame = Instance.new("Frame")
    notifFrame.Parent = notifContainer
    notifFrame.BackgroundColor3 = Color3.fromRGB(255, 255, 255)
    notifFrame.BorderSizePixel = 0
    notifFrame.Size = UDim2.new(1, 0, 0, 90)
    notifFrame.ClipsDescendants = true
    notifFrame.Position = UDim2.new(1, 50, 0, 0)
    notifFrame.ZIndex = 1000
    local corner = Instance.new("UICorner")
    corner.CornerRadius = UDim.new(0, 10)
    corner.Parent = notifFrame
    local gradient = Instance.new("UIGradient")
    gradient.Color = ColorSequence.new{
        ColorSequenceKeypoint.new(0, Color3.fromRGB(240, 240, 245)),
        ColorSequenceKeypoint.new(1, Color3.fromRGB(230, 230, 235))
    }
    gradient.Rotation = 45
    gradient.Parent = notifFrame
    local barra = Instance.new("Frame")
    barra.Parent = notifFrame
    barra.BackgroundColor3 = Color3.fromRGB(200, 40, 40)
    barra.BorderSizePixel = 0
    barra.Size = UDim2.new(0, 5, 1, 0)
    barra.ZIndex = 1001
    local barraCorner = Instance.new("UICorner")
    barraCorner.CornerRadius = UDim.new(0, 10)
    barraCorner.Parent = barra
    local barraGradient = Instance.new("UIGradient")
    barraGradient.Color = ColorSequence.new{
        ColorSequenceKeypoint.new(0, Color3.fromRGB(200, 40, 40)),
        ColorSequenceKeypoint.new(1, Color3.fromRGB(255, 80, 80))
    }
    barraGradient.Rotation = 90
    barraGradient.Parent = barra
    local iconFrame = Instance.new("Frame")
    iconFrame.Parent = notifFrame
    iconFrame.BackgroundColor3 = Color3.fromRGB(200, 40, 40)
    iconFrame.Position = UDim2.new(0, 15, 0, 12)
    iconFrame.Size = UDim2.new(0, 40, 0, 40)
    iconFrame.ZIndex = 1001
    local iconCorner = Instance.new("UICorner")
    iconCorner.CornerRadius = UDim.new(0, 8)
    iconCorner.Parent = iconFrame
    local iconLabel = Instance.new("TextLabel")
    iconLabel.Parent = iconFrame
    iconLabel.BackgroundTransparency = 1
    iconLabel.Size = UDim2.new(1, 0, 1, 0)
    iconLabel.Font = Enum.Font.GothamBold
    iconLabel.Text = "G"
    iconLabel.TextColor3 = Color3.fromRGB(255, 255, 255)
    iconLabel.TextSize = 24
    iconLabel.ZIndex = 1002
    local tituloLabel = Instance.new("TextLabel")
    tituloLabel.Parent = notifFrame
    tituloLabel.BackgroundTransparency = 1
    tituloLabel.Position = UDim2.new(0, 65, 0, 12)
    tituloLabel.Size = UDim2.new(1, -75, 0, 22)
    tituloLabel.Font = Enum.Font.GothamBold
    tituloLabel.Text = titleMsg
    tituloLabel.TextColor3 = Color3.fromRGB(0, 0, 0)
    tituloLabel.TextSize = 15
    tituloLabel.TextXAlignment = Enum.TextXAlignment.Left
    tituloLabel.ZIndex = 1002
    local textoLabel = Instance.new("TextLabel")
    textoLabel.Parent = notifFrame
    textoLabel.BackgroundTransparency = 1
    textoLabel.Position = UDim2.new(0, 65, 0, 38)
    textoLabel.Size = UDim2.new(1, -75, 0, 40)
    textoLabel.Font = Enum.Font.Gotham
    textoLabel.Text = text
    textoLabel.TextColor3 = Color3.fromRGB(80, 80, 90)
    textoLabel.TextSize = 13
    textoLabel.TextXAlignment = Enum.TextXAlignment.Left
    textoLabel.TextYAlignment = Enum.TextYAlignment.Top
    textoLabel.TextWrapped = true
    textoLabel.ZIndex = 1002
    local progressBar = Instance.new("Frame")
    progressBar.Parent = notifFrame
    progressBar.BackgroundColor3 = Color3.fromRGB(200, 40, 40)
    progressBar.BorderSizePixel = 0
    progressBar.Position = UDim2.new(0, 10, 1, -4)
    progressBar.Size = UDim2.new(1, -20, 0, 3)
    progressBar.ZIndex = 1001
    local progressCorner = Instance.new("UICorner")
    progressCorner.CornerRadius = UDim.new(1, 0)
    progressCorner.Parent = progressBar

    notifFrame:TweenPosition(
        UDim2.new(1, 0, 0, 0),
        Enum.EasingDirection.Out,
        Enum.EasingStyle.Back,
        0.6,
        true
    )
    progressBar:TweenSize(
        UDim2.new(0, 0, 0, 3),
        Enum.EasingDirection.InOut,
        Enum.EasingStyle.Linear,
        duration,
        true
    )
    task.delay(duration, function()
        notifFrame:TweenPosition(
            UDim2.new(1, 350, 0, 0),
            Enum.EasingDirection.In,
            Enum.EasingStyle.Quad,
            0.4,
            true,
            function()
                notifFrame:Destroy()
            end
        )
    end)
end

-- =========================
-- SISTEMA DE OPÇÕES E CONTROLES
-- =========================
local options = {}
local toggleStates = {}
local currentTab = "Inicio"

local function createToggle(name, callback)
    local toggleFrame = Instance.new("Frame")
    toggleFrame.Parent = contentFrame
    toggleFrame.BackgroundColor3 = Color3.fromRGB(240, 240, 245)
    toggleFrame.BackgroundTransparency = 0.2
    toggleFrame.Size = UDim2.new(0.95, 0, 0, buttonHeight)
    Instance.new("UICorner", toggleFrame).CornerRadius = UDim.new(0, 8)
    local label = Instance.new("TextLabel")
    label.Parent = toggleFrame
    label.BackgroundTransparency = 1
    label.Position = UDim2.new(0.03, 0, 0, 0)
    label.Size = UDim2.new(0.7, 0, 1, 0)
    label.Font = Enum.Font.Gotham
    label.Text = name
    label.TextColor3 = Color3.fromRGB(0, 0, 0)
    label.TextSize = fontSize
    label.TextXAlignment = Enum.TextXAlignment.Left
    local btn = Instance.new("TextButton")
    btn.Parent = toggleFrame
    btn.BackgroundColor3 = Color3.fromRGB(200, 60, 60)
    btn.Position = UDim2.new(0.82, 0, 0.15, 0)
    btn.Size = UDim2.new(0, 45 * scaleMultiplier, 0, 22 * scaleMultiplier)
    btn.Font = Enum.Font.GothamBold
    btn.Text = "OFF"
    btn.TextColor3 = Color3.fromRGB(255, 255, 255)
    btn.TextSize = 11
    Instance.new("UICorner", btn).CornerRadius = UDim.new(0, 10)
    local toggleKey = currentTab .. "_" .. name
    local state = toggleStates[toggleKey] or false
    if state then
        btn.BackgroundColor3 = Color3.fromRGB(0, 180, 0)
        btn.Text = "ON"
    end
    local enabled = state
    btn.MouseButton1Click:Connect(function()
        enabled = not enabled
        btn.BackgroundColor3 = enabled and Color3.fromRGB(0, 180, 0) or Color3.fromRGB(200, 60, 60)
        btn.Text = enabled and "ON" or "OFF"
        toggleStates[toggleKey] = enabled
        if callback then pcall(callback, enabled) end
    end)
    table.insert(options, {frame = toggleFrame, name = name, tab = currentTab})
    contentFrame.CanvasSize = UDim2.new(0, 0, 0, contentLayout.AbsoluteContentSize.Y)
end

local function createSlider(name, min, max, callback)
    local sliderFrame = Instance.new("Frame")
    sliderFrame.Parent = contentFrame
    sliderFrame.BackgroundColor3 = Color3.fromRGB(240, 240, 245)
    sliderFrame.BackgroundTransparency = 0.2
    sliderFrame.Size = UDim2.new(0.95, 0, 0, 70 * scaleMultiplier)
    Instance.new("UICorner", sliderFrame).CornerRadius = UDim.new(0, 8)
    local label = Instance.new("TextLabel")
    label.Parent = sliderFrame
    label.BackgroundTransparency = 1
    label.Position = UDim2.new(0.03, 0, 0, 5)
    label.Size = UDim2.new(0.7, 0, 0, 20)
    label.Font = Enum.Font.Gotham
    label.Text = name
    label.TextColor3 = Color3.fromRGB(0, 0, 0)
    label.TextSize = fontSize
    local valueLabel = Instance.new("TextLabel")
    valueLabel.Parent = sliderFrame
    valueLabel.BackgroundTransparency = 1
    valueLabel.Position = UDim2.new(0.75, 0, 0, 5)
    valueLabel.Size = UDim2.new(0.22, 0, 0, 20)
    valueLabel.Font = Enum.Font.GothamBold
    valueLabel.Text = tostring(min)
    valueLabel.TextColor3 = Color3.fromRGB(200, 40, 40)
    valueLabel.TextSize = fontSize
    local sliderBack = Instance.new("Frame")
    sliderBack.Parent = sliderFrame
    sliderBack.BackgroundColor3 = Color3.fromRGB(220, 220, 225)
    sliderBack.Position = UDim2.new(0.05, 0, 0.5, 0)
    sliderBack.Size = UDim2.new(0.9, 0, 0, 6)
    Instance.new("UICorner", sliderBack).CornerRadius = UDim.new(1, 0)
    local sliderFill = Instance.new("Frame")
    sliderFill.Parent = sliderBack
    sliderFill.BackgroundColor3 = Color3.fromRGB(200, 40, 40)
    sliderFill.Size = UDim2.new(0, 0, 1, 0)
    Instance.new("UICorner", sliderFill).CornerRadius = UDim.new(1, 0)
    local sliderButton = Instance.new("TextButton")
    sliderButton.Parent = sliderBack
    sliderButton.BackgroundColor3 = Color3.fromRGB(255, 255, 255)
    sliderButton.Position = UDim2.new(0, -6, 0.5, -6)
    sliderButton.Size = UDim2.new(0, 12 * scaleMultiplier, 0, 12 * scaleMultiplier)
    sliderButton.Text = ""
    Instance.new("UICorner", sliderButton).CornerRadius = UDim.new(1, 0)
    local dragging = false
    sliderButton.InputBegan:Connect(function(input)
        if input.UserInputType == Enum.UserInputType.MouseButton1 or input.UserInputType == Enum.UserInputType.Touch then
            dragging = true
        end
    end)
    UserInputService.InputEnded:Connect(function(input)
        if input.UserInputType == Enum.UserInputType.MouseButton1 or input.UserInputType == Enum.UserInputType.Touch then
            dragging = false
        end
    end)
    UserInputService.InputChanged:Connect(function(input)
        if dragging and (input.UserInputType == Enum.UserInputType.MouseMovement or input.UserInputType == Enum.UserInputType.Touch) then
            local mousePos = UserInputService:GetMouseLocation().X
            local sliderPos = sliderBack.AbsolutePosition.X
            local sliderSize = sliderBack.AbsoluteSize.X
            local relativePos = math.clamp((mousePos - sliderPos) / sliderSize, 0, 1)
            local value = math.floor(min + (max - min) * relativePos)
            sliderFill.Size = UDim2.new(relativePos, 0, 1, 0)
            sliderButton.Position = UDim2.new(relativePos, -6, 0.5, -6)
            valueLabel.Text = tostring(value)
            if callback then pcall(callback, value) end
        end
    end)
    table.insert(options, {frame = sliderFrame, name = name, tab = currentTab})
    contentFrame.CanvasSize = UDim2.new(0, 0, 0, contentLayout.AbsoluteContentSize.Y)
end

local function createDropdown(name, items, callback)
    local dropdownFrame = Instance.new("Frame")
    dropdownFrame.Parent = contentFrame
    dropdownFrame.BackgroundColor3 = Color3.fromRGB(240, 240, 245)
    dropdownFrame.BackgroundTransparency = 0.2
    dropdownFrame.Size = UDim2.new(0.95, 0, 0, 50 * scaleMultiplier)
    dropdownFrame.ClipsDescendants = true
    Instance.new("UICorner", dropdownFrame).CornerRadius = UDim.new(0, 8)
    local label = Instance.new("TextLabel")
    label.Parent = dropdownFrame
    label.BackgroundTransparency = 1
    label.Position = UDim2.new(0.03, 0, 0, 5)
    label.Size = UDim2.new(0.6, 0, 0, 35)
    label.Font = Enum.Font.Gotham
    label.Text = name
    label.TextColor3 = Color3.fromRGB(0, 0, 0)
    label.TextSize = fontSize
    local btn = Instance.new("TextButton")
    btn.Parent = dropdownFrame
    btn.BackgroundColor3 = Color3.fromRGB(220, 220, 225)
    btn.Position = UDim2.new(0.55, 0, 0.15, 0)
    btn.Size = UDim2.new(0.4, 0, 0, 30 * scaleMultiplier)
    btn.Font = Enum.Font.Gotham
    btn.Text = items[1] or "Selecione"
    btn.TextColor3 = Color3.fromRGB(60, 60, 70)
    btn.TextSize = 12
    Instance.new("UICorner", btn).CornerRadius = UDim.new(0, 6)
    local open = false
    btn.MouseButton1Click:Connect(function()
        open = not open
        dropdownFrame.Size = open and UDim2.new(0.95, 0, 0, 50 + (#items * 35 * scaleMultiplier)) or UDim2.new(0.95, 0, 0, 50 * scaleMultiplier)
        for i, itemBtn in ipairs(dropdownFrame:GetChildren()) do
            if itemBtn:IsA("TextButton") and itemBtn ~= btn then
                itemBtn.Visible = open
            end
        end
        contentFrame.CanvasSize = UDim2.new(0, 0, 0, contentLayout.AbsoluteContentSize.Y)
    end)
    for i, item in ipairs(items) do
        local itemBtn = Instance.new("TextButton")
        itemBtn.Parent = dropdownFrame
        itemBtn.BackgroundColor3 = Color3.fromRGB(230, 230, 235)
        itemBtn.Position = UDim2.new(0.55, 0, 0, 50 + ((i-1) * 35 * scaleMultiplier))
        itemBtn.Size = UDim2.new(0.4, 0, 0, 30 * scaleMultiplier)
        itemBtn.Font = Enum.Font.Gotham
        itemBtn.Text = item
        itemBtn.TextColor3 = Color3.fromRGB(60, 60, 70)
        itemBtn.TextSize = 12
        itemBtn.Visible = false
        Instance.new("UICorner", itemBtn).CornerRadius = UDim.new(0, 6)
        itemBtn.MouseButton1Click:Connect(function()
            btn.Text = item
            open = false
            dropdownFrame.Size = UDim2.new(0.95, 0, 0, 50 * scaleMultiplier)
            for _, otherBtn in ipairs(dropdownFrame:GetChildren()) do
                if otherBtn:IsA("TextButton") and otherBtn ~= btn then
                    otherBtn.Visible = false
                end
            end
            if callback then pcall(callback, item, i) end
            contentFrame.CanvasSize = UDim2.new(0, 0, 0, contentLayout.AbsoluteContentSize.Y)
        end)
    end
    table.insert(options, {frame = dropdownFrame, name = name, tab = currentTab})
    contentFrame.CanvasSize = UDim2.new(0, 0, 0, contentLayout.AbsoluteContentSize.Y)
end

local function makeDraggable(frame)
    local dragging, dragStart, startPos
    frame.InputBegan:Connect(function(input)
        if input.UserInputType == Enum.UserInputType.MouseButton1 or input.UserInputType == Enum.UserInputType.Touch then
            dragging = true
            dragStart = input.Position
            startPos = frame.Position
        end
    end)
    frame.InputEnded:Connect(function(input)
        if input.UserInputType == Enum.UserInputType.MouseButton1 or input.UserInputType == Enum.UserInputType.Touch then
            dragging = false
        end
    end)
    UserInputService.InputChanged:Connect(function(input)
        if dragging and (input.UserInputType == Enum.UserInputType.MouseMovement or input.UserInputType == Enum.UserInputType.Touch) then
            local delta = input.Position - dragStart
            frame.Position = UDim2.new(startPos.X.Scale, startPos.X.Offset + delta.X, startPos.Y.Scale, startPos.Y.Offset + delta.Y)
        end
    end)
end
makeDraggable(dragFrame)

local function createTabButton(name, iconId)
    local button = Instance.new("TextButton")
    button.Parent = tabContainer
    button.BackgroundColor3 = Color3.fromRGB(230, 230, 235)
    button.BackgroundTransparency = 0.3
    button.Size = UDim2.new(0.9, 0, 0, 35 * scaleMultiplier)
    button.Font = Enum.Font.Gotham
    button.Text = name
    button.TextColor3 = Color3.fromRGB(60, 60, 70)
    button.TextSize = fontSize - 1
    Instance.new("UICorner", button).CornerRadius = UDim.new(0, 6)
    local icon = Instance.new("ImageLabel")
    icon.Parent = button
    icon.BackgroundTransparency = 1
    icon.Position = UDim2.new(0.05, 0, 0.2, 0)
    icon.Size = UDim2.new(0, 20 * scaleMultiplier, 0, 20 * scaleMultiplier)
    icon.Image = iconId
    icon.ImageColor3 = Color3.fromRGB(200, 40, 40)
    button.MouseButton1Click:Connect(function()
        currentTab = name
        tabName.Text = name
        for _, opt in ipairs(options) do
            opt.frame.Visible = (opt.tab == currentTab)
        end
        contentFrame.CanvasSize = UDim2.new(0, 0, 0, contentLayout.AbsoluteContentSize.Y)
        notify("GANK", "Aba selecionada: " .. name, 1.8)
    end)
    tabContainer.CanvasSize = UDim2.new(0, 0, 0, tabLayout.AbsoluteContentSize.Y)
    return button
end

-- =========================
-- SISTEMAS DE JOGABILIDADE (Nitro, Fly, Speed, etc)
-- =========================
local LocalPlayer = Players.LocalPlayer
local Character = LocalPlayer.Character or LocalPlayer.CharacterAdded:Wait()
local HRP = Character:WaitForChild("HumanoidRootPart")
local nitroForce = 50
local nitroConnection

local function getCurrentVehicleSeat()
    local character = Players.LocalPlayer.Character
    if not character or not character:FindFirstChild("Humanoid") then return nil end
    local humanoid = character.Humanoid
    if humanoid.SeatPart and humanoid.SeatPart:IsA("VehicleSeat") then
        return humanoid.SeatPart
    end
    return nil
end

local function getCarFromSeat(seat)
    if not seat then return nil end
    local car = seat.Parent
    while car and car ~= workspace do
        local seats, parts = 0, 0
        for _, child in pairs(car:GetChildren()) do
            if child:IsA("VehicleSeat") or child:IsA("Seat") then
                seats = seats + 1
            elseif child:IsA("BasePart") then
                parts = parts + 1
            end
        end
        if seats >= 1 and parts >= 3 then
            return car
        end
        car = car.Parent
    end
    return seat.Parent
end

local function nitroMethod1(seat)
    local car = getCarFromSeat(seat)
    if not car then return false end
    local success = pcall(function()
        local mainPart = nil
        local maxSize = 0
        for _, part in pairs(car:GetChildren()) do
            if part:IsA("BasePart") and part ~= seat then
                local size = part.Size.Magnitude
                if size > maxSize then
                    maxSize = size
                    mainPart = part
                end
            end
        end
        if not mainPart then mainPart = seat end
        for _, obj in pairs(mainPart:GetChildren()) do
            if obj:IsA("BodyVelocity") then obj:Destroy() end
        end
        local bodyVel = Instance.new("BodyVelocity")
        bodyVel.MaxForce = Vector3.new(4000, 0, 4000)
        bodyVel.Velocity = mainPart.CFrame.LookVector * nitroForce
        bodyVel.Parent = mainPart
        game:GetService("Debris"):AddItem(bodyVel, 0.5)
    end)
    return success
end

local function nitroMethod2(seat)
    local car = getCarFromSeat(seat)
    if not car then return false end
    local success = pcall(function()
        for _, part in pairs(car:GetChildren()) do
            if part:IsA("BasePart") then
                local currentVel = part.AssemblyLinearVelocity
                part.AssemblyLinearVelocity = currentVel + (part.CFrame.LookVector * nitroForce)
            end
        end
    end)
    return success
end

local function nitroMethod3(seat)
    local success = pcall(function()
        local bodyVel = Instance.new("BodyVelocity")
        bodyVel.MaxForce = Vector3.new(math.huge, 0, math.huge)
        bodyVel.Velocity = seat.CFrame.LookVector * nitroForce
        bodyVel.Parent = seat
        game:GetService("Debris"):AddItem(bodyVel, 0.3)
    end)
    return success
end

local function activateNitro()
    local seat = getCurrentVehicleSeat()
    if not seat then
        notify("Nitro", "Entre em um veículo primeiro!", 3)
        return
    end
    nitroMethod1(seat)
    task.wait(0.1)
    nitroMethod2(seat)
    task.wait(0.1)
    nitroMethod3(seat)
    notify("Nitro", "Nitro ativado!", 2)
end

local function toggleNitro(state)
    if state then
        if nitroConnection then
            if isMobile and nitroConnection:IsA("TextButton") then
                nitroConnection:Destroy()
            elseif not isMobile then
                nitroConnection:Disconnect()
            end
        end
        if isMobile then
            local nitroButton = Instance.new("TextButton")
            nitroButton.Name = "NitroButtonMobile"
            nitroButton.Parent = screenGui
            nitroButton.Size = UDim2.new(0, 80, 0, 80)
            nitroButton.Position = UDim2.new(1, -100, 1, -100)
            nitroButton.BackgroundColor3 = Color3.fromRGB(255, 0, 0)
            nitroButton.Text = "NITRO"
            nitroButton.TextColor3 = Color3.fromRGB(255, 255, 255)
            nitroButton.TextSize = 16
            nitroButton.ZIndex = 1000
            Instance.new("UICorner", nitroButton).CornerRadius = UDim.new(0, 20)
            nitroButton.MouseButton1Click:Connect(function()
                activateNitro()
            end)
            nitroConnection = nitroButton
        else
            nitroConnection = UserInputService.InputBegan:Connect(function(input, gameProcessed)
                if gameProcessed then return end
                if input.KeyCode == Enum.KeyCode.N then
                    activateNitro()
                end
            end)
        end
        notify("Nitro", "Nitro automático ativado!", 3)
    else
        if nitroConnection then
            if isMobile and nitroConnection:IsA("TextButton") then
                nitroConnection:Destroy()
            elseif not isMobile then
                nitroConnection:Disconnect()
            end
            nitroConnection = nil
        end
        notify("Nitro", "Nitro automático desativado!", 3)
    end
end

-- TELEPORT PLAYER (nova opção)
local function teleportToPlayer(targetPlayer)
    if not targetPlayer or not targetPlayer.Character or not targetPlayer.Character:FindFirstChild("HumanoidRootPart") then
        notify("Teleport", "Jogador inválido ou sem personagem.", 3)
        return
    end
    local hrp = targetPlayer.Character:FindFirstChild("HumanoidRootPart")
    if hrp then
        local player = Players.LocalPlayer
        local char = player.Character or player.CharacterAdded:Wait()
        local myHrp = char:WaitForChild("HumanoidRootPart")
        myHrp.CFrame = hrp.CFrame + Vector3.new(0, 3, 0)
        notify("Teleport", "Teleportado para " .. targetPlayer.Name, 3)
    end
end

-- SPEEDHACK
local speedHackEnabled = false
local defaultWalkSpeed = 16
local currentWalkSpeed = defaultWalkSpeed
local function setWalkSpeed(value)
    currentWalkSpeed = value
    local char = LocalPlayer.Character
    if char then
        local humanoid = char:FindFirstChildOfClass("Humanoid")
        if humanoid then
            humanoid.WalkSpeed = value
        end
    end
end

-- =========================
-- AIMBOT / ESP / TRACERS (expansão ESP)
-- =========================
local FOV_RADIUS = 35
local FOV_COLOR = Color3.fromRGB(56, 139, 253)
local AIM_PART = "Head"
local TEAM_CHECK = true
local AimbotActive = false

local fovCircle = Drawing and Drawing.new and Drawing.new("Circle") or nil
if fovCircle then
    fovCircle.Visible = false
    fovCircle.Color = FOV_COLOR
    fovCircle.Radius = FOV_RADIUS
    fovCircle.Thickness = 2
    fovCircle.Filled = false
    fovCircle.Position = Vector2.new(Camera.ViewportSize.X / 2, Camera.ViewportSize.Y / 2)
end

local function getClosestTarget()
    local closest, minDist = nil, FOV_RADIUS
    for _, p in ipairs(Players:GetPlayers()) do
        if p ~= LocalPlayer and p.Character and p.Character:FindFirstChild(AIM_PART) then
            if not TEAM_CHECK or p.Team ~= LocalPlayer.Team then
                local part = p.Character[AIM_PART]
                local pos, onscreen = Camera:WorldToViewportPoint(part.Position)
                if onscreen then
                    local dist = (Vector2.new(pos.X, pos.Y) - (fovCircle and fovCircle.Position or Vector2.new(Camera.ViewportSize.X/2, Camera.ViewportSize.Y/2))).Magnitude
                    if dist < minDist then
                        minDist = dist
                        closest = part
                    end
                end
            end
        end
    end
    return closest
end

local function aimAt(part)
    if not part then return end
    local camPos = Camera.CFrame.Position
    local dir = (part.Position - camPos).Unit
    Camera.CFrame = CFrame.new(camPos, camPos + dir)
end

local aimbotConn
local function startAimbot()
    if aimbotConn then aimbotConn:Disconnect() end
    aimbotConn = RunService.RenderStepped:Connect(function()
        if AimbotActive then
            local target = getClosestTarget()
            if target then aimAt(target) end
        end
    end)
end
startAimbot()

-- ESP improvements
local espEnabled = false
local espBoxColor = Color3.fromRGB(255, 0, 0)
local espHealthColorFull = Color3.fromRGB(0, 255, 0)
local espHealthColorEmpty = Color3.fromRGB(255, 0, 0)
local espTextColor = Color3.fromRGB(255, 255, 255)
local ESPs = {}
local espShowNames = true
local espShowDistance = true
local espShowSkeleton = false
local espMaxDistance = 200

local function createESP(player)
    if player == LocalPlayer then return end
    local function OnCharacterAdded(character)
        local humanoid = character:WaitForChild("Humanoid")
        local rootPart = character:WaitForChild("HumanoidRootPart")
        if ESPs[player] then
            for _, drawing in pairs(ESPs[player]) do
                if drawing and drawing.Remove then
                    pcall(function() drawing:Remove() end)
                end
            end
        end

        local box = Drawing and Drawing.new("Square") or nil
        if box then
            box.Visible = false
            box.Filled = false
            box.Thickness = 2
            box.Color = espBoxColor
        end
        local healthBar = Drawing and Drawing.new("Square") or nil
        if healthBar then
            healthBar.Visible = false
            healthBar.Filled = true
            healthBar.Color = espHealthColorFull
        end
        local nameText = Drawing and Drawing.new("Text") or nil
        if nameText then
            nameText.Visible = false
            nameText.Color = espTextColor
            nameText.Size = 16
            nameText.Center = true
            nameText.Outline = true
            nameText.Font = 2
        end
        local healthText = Drawing and Drawing.new("Text") or nil
        if healthText then
            healthText.Visible = false
            healthText.Color = espTextColor
            healthText.Size = 14
            healthText.Center = true
            healthText.Outline = true
            healthText.Font = 2
        end
        local skeletonLines = {}
        ESPs[player] = {
            box = box,
            healthBar = healthBar,
            nameText = nameText,
            healthText = healthText,
            skeletonLines = skeletonLines
        }
    end
    if player.Character then
        OnCharacterAdded(player.Character)
    end
    player.CharacterAdded:Connect(OnCharacterAdded)
end

local function drawSkeletonForPlayer(player, drawings)
    if not drawings or not drawings.skeletonLines then return end
    local char = player.Character
    if not char then return end
    local parts = {
        head = char:FindFirstChild("Head"),
        torso = char:FindFirstChild("UpperTorso") or char:FindFirstChild("Torso") or char:FindFirstChild("HumanoidRootPart"),
        leftArm = char:FindFirstChild("LeftUpperArm") or char:FindFirstChild("Left Arm"),
        rightArm = char:FindFirstChild("RightUpperArm") or char:FindFirstChild("Right Arm"),
        leftLeg = char:FindFirstChild("LeftUpperLeg") or char:FindFirstChild("Left Leg"),
        rightLeg = char:FindFirstChild("RightUpperLeg") or char:FindFirstChild("Right Leg"),
    }
    local joints = {
        {"head", "torso"},
        {"torso", "leftArm"},
        {"torso", "rightArm"},
        {"torso", "leftLeg"},
        {"torso", "rightLeg"},
    }
    -- ensure we have enough line objects
    for i = 1, #joints do
        if not drawings.skeletonLines[i] then
            local ln = Drawing and Drawing.new("Line") or nil
            if ln then
                ln.Visible = false
                ln.Thickness = 2
                ln.Color = espBoxColor
            end
            drawings.skeletonLines[i] = ln
        end
    end

    for i, pair in ipairs(joints) do
        local a = parts[pair[1]]
        local b = parts[pair[2]]
        local ln = drawings.skeletonLines[i]
        if a and b and ln then
            local apos, aOn = Camera:WorldToViewportPoint(a.Position)
            local bpos, bOn = Camera:WorldToViewportPoint(b.Position)
            if aOn and bOn then
                ln.From = Vector2.new(apos.X, apos.Y)
                ln.To = Vector2.new(bpos.X, bpos.Y)
                ln.Visible = true
            else
                ln.Visible = false
            end
        elseif ln then
            ln.Visible = false
        end
    end
end

local function updateESP()
    if not ESPs then return end
    if not espEnabled then
        for _, drawings in pairs(ESPs) do
            for k, drawing in pairs(drawings) do
                if drawing and drawing.Visible ~= nil then
                    drawing.Visible = false
                elseif type(drawing) == "table" then
                    for _, sub in pairs(drawing) do
                        if sub and sub.Visible ~= nil then sub.Visible = false end
                    end
                end
            end
        end
        return
    end
    local localCharacter = LocalPlayer.Character
    local localRootPart = localCharacter and localCharacter:FindFirstChild("HumanoidRootPart")
    for player, drawings in pairs(ESPs) do
        local character = player.Character
        if character and character:FindFirstChild("HumanoidRootPart") and character:FindFirstChild("Humanoid") then
            local humanoid = character.Humanoid
            local rootPart = character.HumanoidRootPart
            local health = humanoid.Health
            local maxHealth = humanoid.MaxHealth
            local healthPercent = (maxHealth > 0) and (health / maxHealth) or 0
            local distance = localRootPart and (localRootPart.Position - rootPart.Position).Magnitude or math.huge
            local screenPos, onScreen = Camera:WorldToViewportPoint(rootPart.Position)
            if onScreen and distance <= espMaxDistance then
                local boxSize = math.clamp(1000 / math.max(distance,1), 10, 120)
                local boxHeight = boxSize * 1.6
                if drawings.box then
                    drawings.box.Size = Vector2.new(boxSize, boxHeight)
                    drawings.box.Position = Vector2.new(screenPos.X - boxSize / 2, screenPos.Y - boxHeight / 2)
                    drawings.box.Color = espBoxColor
                    drawings.box.Visible = true
                end
                if drawings.healthBar then
                    local barX = screenPos.X - boxSize / 2 - 8
                    local barY = screenPos.Y - boxHeight / 2
                    local healthBarHeight = boxHeight * healthPercent
                    drawings.healthBar.Size = Vector2.new(6, healthBarHeight)
                    drawings.healthBar.Position = Vector2.new(barX, barY + boxHeight * (1 - healthPercent))
                    drawings.healthBar.Color = espHealthColorFull:lerp(espHealthColorEmpty, 1 - healthPercent)
                    drawings.healthBar.Visible = true
                end
                if drawings.nameText and espShowNames then
                    drawings.nameText.Text = player.Name
                    drawings.nameText.Position = Vector2.new(screenPos.X, screenPos.Y - boxHeight / 2 - 20)
                    drawings.nameText.Visible = true
                elseif drawings.nameText then
                    drawings.nameText.Visible = false
                end
                if drawings.healthText and espShowDistance then
                    drawings.healthText.Text = math.floor(health) .. " | " .. math.floor(distance) .. "m"
                    drawings.healthText.Position = Vector2.new(screenPos.X, screenPos.Y - boxHeight / 2 - 40)
                    drawings.healthText.Visible = true
                elseif drawings.healthText then
                    drawings.healthText.Visible = false
                end
                if espShowSkeleton then
                    drawSkeletonForPlayer(player, drawings)
                else
                    if drawings.skeletonLines then
                        for _, ln in pairs(drawings.skeletonLines) do
                            if ln then ln.Visible = false end
                        end
                    end
                end
            else
                -- hide
                if drawings.box then drawings.box.Visible = false end
                if drawings.healthBar then drawings.healthBar.Visible = false end
                if drawings.nameText then drawings.nameText.Visible = false end
                if drawings.healthText then drawings.healthText.Visible = false end
                if drawings.skeletonLines then
                    for _, ln in pairs(drawings.skeletonLines) do
                        if ln then ln.Visible = false end
                    end
                end
            end
        else
            -- hide
            if drawings.box then drawings.box.Visible = false end
            if drawings.healthBar then drawings.healthBar.Visible = false end
            if drawings.nameText then drawings.nameText.Visible = false end
            if drawings.healthText then drawings.healthText.Visible = false end
            if drawings.skeletonLines then
                for _, ln in pairs(drawings.skeletonLines) do
                    if ln then ln.Visible = false end
                end
            end
        end
    end
end

-- Tracers (já presente, expandidos)
local tracerEnabled = false
local tracerColor = Color3.fromRGB(0, 255, 0)
local tracers = {}
local function createTracer(player)
    if player == LocalPlayer or tracers[player] then return end
    local line = Drawing and Drawing.new("Line") or nil
    if line then
        line.Visible = false
        line.Color = tracerColor
        line.Thickness = 2
        tracers[player] = line
    end
end
local function removeTracer(player)
    if tracers[player] then
        pcall(function() tracers[player]:Remove() end)
        tracers[player] = nil
    end
end
local function updateTracers()
    if not tracerEnabled then
        for _, line in pairs(tracers) do
            if line then line.Visible = false end
        end
        return
    end
    local viewportCenter = Camera.ViewportSize / 2
    for player, line in pairs(tracers) do
        local char = player.Character
        if char and char:FindFirstChild("HumanoidRootPart") then
            local hrp = char.HumanoidRootPart
            local pos, onScreen = Camera:WorldToViewportPoint(hrp.Position)
            if onScreen then
                line.From = Vector2.new(viewportCenter.X, viewportCenter.Y)
                line.To = Vector2.new(pos.X, pos.Y)
                line.Color = tracerColor
                line.Visible = true
            else
                line.Visible = false
            end
        else
            line.Visible = false
        end
    end
end

-- =========================
-- INFINITE AMMO, AUTO FARM, FLY (mantidos)
-- =========================
local infiniteAmmoFuzilConn
local infiniteAmmoFuzilConns = {}
local function toggleInfiniteAmmoFuzil(state)
    if state then
        local function checkValue(obj)
            if obj:IsA("IntValue") or obj:IsA("NumberValue") then
                if obj.Value == 30 then
                    obj.Value = 999
                end
                local conn = obj.Changed:Connect(function()
                    if obj.Value == 30 then
                        obj.Value = 999
                    end
                end)
                table.insert(infiniteAmmoFuzilConns, conn)
            end
        end
        for _, descendant in ipairs(workspace:GetDescendants()) do
            pcall(function() checkValue(descendant) end)
        end
        infiniteAmmoFuzilConn = workspace.DescendantAdded:Connect(function(obj)
            pcall(function() checkValue(obj) end)
        end)
        LocalPlayer.CharacterAdded:Connect(function()
            task.wait(1)
            for _, descendant in ipairs(workspace:GetDescendants()) do
                pcall(function() checkValue(descendant) end)
            end
        end)
        notify("Munição Infinita", "Munição infinita (Fuzil) ATIVADA!", 3)
    else
        if infiniteAmmoFuzilConn then
            infiniteAmmoFuzilConn:Disconnect()
            infiniteAmmoFuzilConn = nil
        end
        for _, conn in ipairs(infiniteAmmoFuzilConns) do
            conn:Disconnect()
        end
        infiniteAmmoFuzilConns = {}
        notify("Munição Infinita", "Munição infinita (Fuzil) DESATIVADA!", 3)
    end
end

local infiniteAmmoOitaoConn
local infiniteAmmoOitaoConns = {}
local function toggleInfiniteAmmoOitao(state)
    if state then
        local function checkValue(obj)
            if obj:IsA("IntValue") or obj:IsA("NumberValue") then
                if obj.Value == 10 then
                    obj.Value = 999
                end
                local conn = obj.Changed:Connect(function()
                    if obj.Value == 10 then
                        obj.Value = 999
                    end
                end)
                table.insert(infiniteAmmoOitaoConns, conn)
            end
        end
        for _, descendant in ipairs(workspace:GetDescendants()) do
            pcall(function() checkValue(descendant) end)
        end
        infiniteAmmoOitaoConn = workspace.DescendantAdded:Connect(function(obj)
            pcall(function() checkValue(obj) end)
        end)
        LocalPlayer.CharacterAdded:Connect(function()
            task.wait(1)
            for _, descendant in ipairs(workspace:GetDescendants()) do
                pcall(function() checkValue(descendant) end)
            end
        end)
        notify("Munição Infinita", "Munição infinita (Oitão) ATIVADA!", 3)
    else
        if infiniteAmmoOitaoConn then
            infiniteAmmoOitaoConn:Disconnect()
            infiniteAmmoOitaoConn = nil
        end
        for _, conn in ipairs(infiniteAmmoOitaoConns) do
            conn:Disconnect()
        end
        infiniteAmmoOitaoConns = {}
        notify("Munição Infinita", "Munição infinita (Oitão) DESATIVADA!", 3)
    end
end

local autoFarmEnabled = false
local function farmLoop()
    if not autoFarmEnabled then return end
    task.spawn(function()
        local player = Players.LocalPlayer
        local character = player.Character or player.CharacterAdded:Wait()
        local hrp = character:WaitForChild("HumanoidRootPart")
        local destinations = {
            CFrame.new(-58.496, 441.087, 138.503),
            CFrame.new(193.740, 439.146, 297.125),
            CFrame.new(24.716, 439.146, 1.296),
            CFrame.new(110.960, 438.382, 204.496),
            CFrame.new(27.037, 439.146, 203.228),
            CFrame.new(-32.853, 438.382, 206.738),
            CFrame.new(-27.927, 439.146, 194.463),
            CFrame.new(-34.799, 439.437, -62.107),
        }
        local tweenInfo = TweenInfo.new(12, Enum.EasingStyle.Linear)
        for _, dest in ipairs(destinations) do
            if not autoFarmEnabled then break end
            local tween = TweenService:Create(hrp, tweenInfo, {CFrame = dest})
            tween:Play()
            tween.Completed:Wait()
            task.wait(1)
        end
    end)
end

local flyEnabled = false
local flyConnection
local function toggleFlyMobile(state)
    if state then
        local player = Players.LocalPlayer
        local character = player.Character or player.CharacterAdded:Wait()
        local humanoid = character:WaitForChild("Humanoid")
        local hrp = character:WaitForChild("HumanoidRootPart")
        local cam = workspace.CurrentCamera
        local speed = 60
        local vertical = 0
        local bv = Instance.new("BodyVelocity")
        bv.MaxForce = Vector3.new(1e5, 1e5, 1e5)
        bv.Velocity = Vector3.zero
        local bg = Instance.new("BodyGyro")
        bg.MaxTorque = Vector3.new(1e5, 1e5, 1e5)
        bg.CFrame = hrp.CFrame
        bg.P = 1e5
        local flyGui = Instance.new("ScreenGui")
        flyGui.Name = "FlyGui"
        flyGui.Parent = player:WaitForChild("PlayerGui")
        flyGui.ResetOnSpawn = false
        local toggleButton = Instance.new("TextButton")
        toggleButton.Size = UDim2.new(0, 120, 0, 45)
        toggleButton.Position = UDim2.new(0, 10, 1, -60)
        toggleButton.Text = "🚀 Fly ON"
        toggleButton.Font = Enum.Font.GothamBold
        toggleButton.TextSize = 20
        toggleButton.BackgroundColor3 = Color3.fromRGB(30, 30, 30)
        toggleButton.TextColor3 = Color3.fromRGB(255, 255, 255)
        toggleButton.BackgroundTransparency = 0.2
        toggleButton.Parent = flyGui
        local function createRoundButton(text, pos)
            local button = Instance.new("TextButton")
            button.Size = UDim2.new(0, 60, 0, 60)
            button.Position = pos
            button.Text = text
            button.Font = Enum.Font.GothamBold
            button.TextSize = 32
            button.BackgroundColor3 = Color3.fromRGB(40, 40, 40)
            button.TextColor3 = Color3.fromRGB(255, 255, 255)
            button.BackgroundTransparency = 0.25
            local corner = Instance.new("UICorner")
            corner.CornerRadius = UDim.new(1, 0)
            corner.Parent = button
            button.Parent = flyGui
            return button
        end
        local upButton = createRoundButton("▲", UDim2.new(1, -150, 1, -150))
        local downButton = createRoundButton("▼", UDim2.new(1, -70, 1, -80))
        upButton.MouseButton1Down:Connect(function() vertical = 1 end)
        upButton.MouseButton1Up:Connect(function() vertical = 0 end)
        downButton.MouseButton1Down:Connect(function() vertical = -1 end)
        downButton.MouseButton1Up:Connect(function() vertical = 0 end)
        flyConnection = RunService.RenderStepped:Connect(function()
            if flyEnabled then
                bv.Parent = hrp
                bg.Parent = hrp
                local moveDir = humanoid.MoveDirection
                bv.Velocity = (moveDir * speed) + Vector3.new(0, vertical * speed, 0)
                bg.CFrame = cam.CFrame
            end
        end)
        player.CharacterAdded:Connect(function(newChar)
            character = newChar
            humanoid = newChar:WaitForChild("Humanoid")
            hrp = newChar:WaitForChild("HumanoidRootPart")
            if flyEnabled then
                bv.Parent = hrp
                bg.Parent = hrp
            end
        end)
        toggleButton.MouseButton1Click:Connect(function()
            flyEnabled = not flyEnabled
            toggleButton.Text = flyEnabled and "🚀 Fly ON" or "🚀 Fly OFF"
            if not flyEnabled then
                bv.Velocity = Vector3.zero
                bv.Parent = nil
                bg.CFrame = hrp.CFrame
                bg.Parent = nil
                vertical = 0
            end
            notify("Fly Mobile", flyEnabled and "Fly Mobile ATIVADO!" or "Fly Mobile DESATIVADO!", 3)
        end)
        notify("Fly Mobile", "Fly Mobile ATIVADO!", 3)
    else
        flyEnabled = false
        if flyConnection then
            flyConnection:Disconnect()
            flyConnection = nil
        end
        local flyGui = Player.PlayerGui:FindFirstChild("FlyGui")
        if flyGui then flyGui:Destroy() end
        notify("Fly Mobile", "Fly Mobile DESATIVADO!", 3)
    end
end

local flyPCEnabled = false
local flyPCConnection
local function toggleFlyPC(state)
    if state then
        local player = Players.LocalPlayer
        local character = player.Character or player.CharacterAdded:Wait()
        local humanoid = character:WaitForChild("Humanoid")
        local hrp = character:WaitForChild("HumanoidRootPart")
        local cam = workspace.CurrentCamera
        local speed = 60
        local vertical = 0
        local bv = Instance.new("BodyVelocity")
        bv.MaxForce = Vector3.new(1e5, 1e5, 1e5)
        bv.Velocity = Vector3.zero
        local bg = Instance.new("BodyGyro")
        bg.MaxTorque = Vector3.new(1e5, 1e5, 1e5)
        bg.CFrame = hrp.CFrame
        bg.P = 1e5
        flyPCConnection = RunService.RenderStepped:Connect(function()
            if flyPCEnabled then
                bv.Parent = hrp
                bg.Parent = hrp
                local moveDir = humanoid.MoveDirection
                bv.Velocity = (moveDir * speed) + Vector3.new(0, vertical * speed, 0)
                bg.CFrame = cam.CFrame
            end
        end)
        UserInputService.InputBegan:Connect(function(input, gameProcessed)
            if gameProcessed then return end
            if input.KeyCode == Enum.KeyCode.E then
                vertical = 1
            elseif input.KeyCode == Enum.KeyCode.Q then
                vertical = -1
            end
        end)
        UserInputService.InputEnded:Connect(function(input)
            if input.KeyCode == Enum.KeyCode.E or input.KeyCode == Enum.KeyCode.Q then
                vertical = 0
            end
        end)
        player.CharacterAdded:Connect(function(newChar)
            character = newChar
            humanoid = newChar:WaitForChild("Humanoid")
            hrp = newChar:WaitForChild("HumanoidRootPart")
            if flyPCEnabled then
                bv.Parent = hrp
                bg.Parent = hrp
            end
        end)
        notify("Fly PC", "Fly PC ATIVADO!", 3)
    else
        flyPCEnabled = false
        if flyPCConnection then
            flyPCConnection:Disconnect()
            flyPCConnection = nil
        end
        if HRP then
            for _, obj in ipairs(HRP:GetChildren()) do
                if obj:IsA("BodyVelocity") or obj:IsA("BodyGyro") then
                    obj:Destroy()
                end
            end
        end
        notify("Fly PC", "Fly PC DESATIVADO!", 3)
    end
end

-- =========================
-- CRIAÇÃO DAS ABAS E OPÇÕES
-- =========================
for _, p in ipairs(Players:GetPlayers()) do
    createESP(p)
    createTracer(p)
end
Players.PlayerAdded:Connect(function(player)
    createESP(player)
    createTracer(player)
end)
Players.PlayerRemoving:Connect(function(player)
    if ESPs[player] then
        for _, drawing in pairs(ESPs[player]) do
            if drawing and drawing.Remove then
                pcall(function() drawing:Remove() end)
            end
        end
        ESPs[player] = nil
    end
    removeTracer(player)
end)

RunService.RenderStepped:Connect(updateESP)
RunService.RenderStepped:Connect(updateTracers)

createTabButton("Inicio", "rbxassetid://117130789916072")
createTabButton("Combate", "rbxassetid://117130789916072")
createTabButton("Aimbots", "rbxassetid://117130789916072")
createTabButton("Visual", "rbxassetid://117130789916072")
createTabButton("Teleport", "rbxassetid://117130789916072")
createTabButton("Outros", "rbxassetid://117130789916072")

-- Aba Inicio
currentTab = "Inicio"
createToggle("Bem-vindo", function(state)
    if state then
        notify("Bem-vindo!", "Obrigado por usar o GANK!", 5)
    end
end)
createToggle("Mostrar Notificações", function(state)
    notificationsEnabled = state
end)

-- Aba Combate
currentTab = "Combate"
createToggle("Nitro Automático", toggleNitro)
createSlider("Força do Nitro", 20, 150, function(value)
    nitroForce = value
    notify("Nitro", "Força ajustada para: " .. value, 2)
end)
createToggle("Munição Infinita (Fuzil)", toggleInfiniteAmmoFuzil)
createToggle("Munição Infinita (Oitão)", toggleInfiniteAmmoOitao)

-- Aba Aimbots
currentTab = "Aimbots"
createToggle("Aimbot Ativo", function(state)
    AimbotActive = state
    if fovCircle then fovCircle.Visible = state end
    notify("Aimbot", state and "Aimbot ATIVADO!" or "Aimbot DESATIVADO!", 3)
end)
createToggle("Team Check", function(state)
    TEAM_CHECK = state
    notify("Aimbot", "Team Check: " .. (state and "ATIVADO" or "DESATIVADO"), 3)
end)
createToggle("Trocar Parte (Head/Torso)", function(state)
    AIM_PART = state and "Torso" or "Head"
    notify("Aimbot", "Mirando no: " .. AIM_PART, 3)
end)
createSlider("FOV", 10, 500, function(value)
    FOV_RADIUS = value
    if fovCircle then fovCircle.Radius = value end
    notify("Aimbot", "FOV ajustado para: " .. value, 2)
end)

-- Aba Visual (ESP)
currentTab = "Visual"
createToggle("ESP Box com Vida", function(state)
    espEnabled = state
    notify("ESP", state and "ESP Box ATIVADO!" or "ESP Box DESATIVADO!", 3)
end)
createToggle("ESP Tracer", function(state)
    tracerEnabled = state
    notify("ESP Tracer", state and "ESP Tracer ATIVADO!" or "ESP Tracer DESATIVADO!", 3)
end)
createToggle("Mostrar Nomes", function(state)
    espShowNames = state
    notify("ESP", state and "Mostrar Nomes ATIVADO" or "Mostrar Nomes DESATIVADO", 3)
end)
createToggle("Mostrar Distância / Vida", function(state)
    espShowDistance = state
    notify("ESP", state and "Mostrar Distância ATIVADO" or "Mostrar Distância DESATIVADO", 3)
end)
createToggle("Skeleton ESP", function(state)
    espShowSkeleton = state
    notify("ESP", state and "Skeleton ATIVADO" or "Skeleton DESATIVADO", 3)
end)
createSlider("ESP Alcance Máx (m)", 50, 500, function(value)
    espMaxDistance = value
    notify("ESP", "Alcance máximo: " .. value .. "m", 2)
end)

-- Aba Teleport (removidas locations fixas, adicionado Teleport Player)
currentTab = "Teleport"
local function buildPlayerListItems()
    local items = {}
    for _, pl in ipairs(Players:GetPlayers()) do
        table.insert(items, pl.Name)
    end
    return items
end

createDropdown("Teleport para Jogador", buildPlayerListItems(), function(item, index)
    local target = Players:FindFirstChild(item)
    if target then
        teleportToPlayer(target)
    else
        notify("Teleport", "Jogador não encontrado.", 3)
    end
end)

-- Atualiza dropdown de jogadores dinamicamente (recria)
Players.PlayerAdded:Connect(function()
    -- reencontrar o dropdown (simplificação: criar um novo)
    -- Nota: em uma versão refinada você atualizaria os botões existentes
end)

-- Aba Outros
currentTab = "Outros"
createToggle("Auto Farm Gari", function(state)
    autoFarmEnabled = state
    notify("Auto Farm", state and "Auto Farm Gari ATIVADO!" or "Auto Farm Gari DESATIVADO!", 3)
    if state then farmLoop() end
end)
createToggle("Fly Mobile", toggleFlyMobile)
createToggle("Fly PC", toggleFlyPC)

-- Speedhack: Slider + Toggle
currentTab = "Outros"
createSlider("Speed (WalkSpeed)", 16, 200, function(value)
    setWalkSpeed(value)
    notify("Speed", "Velocidade ajustada para: " .. value, 2)
end)
createToggle("Speedhack Ativo", function(state)
    speedHackEnabled = state
    if not state then
        setWalkSpeed(defaultWalkSpeed)
        notify("Speed", "Speedhack DESATIVADO. WalkSpeed restaurado.", 2)
    else
        -- assegura aplicar o valor atual
        setWalkSpeed(currentWalkSpeed)
        notify("Speed", "Speedhack ATIVADO.", 2)
    end
end)

-- Aplica visibilidade inicial das opções de acordo com aba atual
currentTab = "Inicio"
for _, opt in ipairs(options) do
    opt.frame.Visible = (opt.tab == currentTab)
end
contentFrame.CanvasSize = UDim2.new(0, 0, 0, contentLayout.AbsoluteContentSize.Y)

-- Minimize / Restore
minimizeBtn.MouseButton1Click:Connect(function()
    if mainContainer.Visible then
        mainContainer.Visible = false
        minimizeBtn.Text = "-"
        if reopenButton then
            reopenButton:Destroy()
            reopenButton = nil
        end
        reopenButton = Instance.new("ImageButton")
        reopenButton.Parent = screenGui
        reopenButton.BackgroundColor3 = Color3.fromRGB(240, 240, 245)
        reopenButton.BackgroundTransparency = 0.1
        reopenButton.Size = UDim2.new(0, 100 * scaleMultiplier, 0, 100 * scaleMultiplier)
        reopenButton.Position = lastReopenButtonPosition or mainContainer.Position
        reopenButton.Image = "rbxassetid://117130789916072"
        reopenButton.ImageColor3 = Color3.fromRGB(200, 40, 40)
        reopenButton.ScaleType = Enum.ScaleType.Fit
        Instance.new("UICorner", reopenButton).CornerRadius = UDim.new(0, 12)
        local stroke = Instance.new("UIStroke", reopenButton)
        stroke.Color = Color3.fromRGB(200, 40, 40)
        stroke.Thickness = 2
        stroke.Transparency = 0.7
        reopenButton.ZIndex = 10
        local dragging, dragStart, startPos
        local isClicking = false
        local clickStartTime = 0
        local clickStartPos = Vector2.new(0, 0)
        local CLICK_THRESHOLD = 0.2
        local MOVE_THRESHOLD = 5
        reopenButton.InputBegan:Connect(function(input)
            if input.UserInputType == Enum.UserInputType.MouseButton1 or input.UserInputType == Enum.UserInputType.Touch then
                dragging = true
                isClicking = true
                dragStart = input.Position
                startPos = reopenButton.Position
                clickStartTime = tick()
                clickStartPos = Vector2.new(input.Position.X, input.Position.Y)
            end
        end)
        reopenButton.InputEnded:Connect(function(input)
            if input.UserInputType == Enum.UserInputType.MouseButton1 or input.UserInputType == Enum.UserInputType.Touch then
                dragging = false
                local clickDuration = tick() - clickStartTime
                local endPos = Vector2.new(input.Position.X, input.Position.Y)
                local moveDistance = (endPos - clickStartPos).Magnitude
                if isClicking and clickDuration < CLICK_THRESHOLD and moveDistance < MOVE_THRESHOLD then
                    mainContainer.Visible = true
                    mainContainer.Size = UDim2.new(0, 750 * scaleMultiplier, 0, 500 * scaleMultiplier)
                    minimizeBtn.Size = UDim2.new(0, 30 * scaleMultiplier, 0, 30 * scaleMultiplier)
                    minimizeBtn.Position = UDim2.new(0.78, 0, 0.15, 0)
                    minimizeBtn.Text = "-"
                    if reopenButton then
                        lastReopenButtonPosition = reopenButton.Position
                        reopenButton:Destroy()
                        reopenButton = nil
                    end
                    local flyGui = Player.PlayerGui:FindFirstChild("FlyGui")
                    if flyGui then flyGui.Visible = true end
                    notify("Janela", "Restaurado", 1.6)
                end
                isClicking = false
            end
        end)
        UserInputService.InputChanged:Connect(function(input)
            if dragging and (input.UserInputType == Enum.UserInputType.MouseMovement or input.UserInputType == Enum.UserInputType.Touch) then
                local delta = input.Position - dragStart
                reopenButton.Position = UDim2.new(startPos.X.Scale, startPos.X.Offset + delta.X, startPos.Y.Scale, startPos.Y.Offset + delta.Y)
                local currentPos = Vector2.new(input.Position.X, input.Position.Y)
                if (currentPos - clickStartPos).Magnitude > MOVE_THRESHOLD then
                    isClicking = false
                end
            end
        end)
        local flyGui = Player.PlayerGui:FindFirstChild("FlyGui")
        if flyGui then flyGui.Visible = false end
        notify("Janela", "Minimizado", 1.6)
    else
        mainContainer.Visible = true
        mainContainer.Size = UDim2.new(0, 750 * scaleMultiplier, 0, 500 * scaleMultiplier)
        minimizeBtn.Size = UDim2.new(0, 30 * scaleMultiplier, 0, 30 * scaleMultiplier)
        minimizeBtn.Position = UDim2.new(0.78, 0, 0.15, 0)
        minimizeBtn.Text = "-"
        if reopenButton then
            lastReopenButtonPosition = reopenButton.Position
            reopenButton:Destroy()
            reopenButton = nil
        end
        local flyGui = Player.PlayerGui:FindFirstChild("FlyGui")
        if flyGui then flyGui.Visible = true end
        notify("Janela", "Restaurado", 1.6)
    end
end)

closeBtn.MouseButton1Click:Connect(function()
    screenGui:Destroy()
end)

-- Notificação inicial
notify("GANK", "GUI carregado com sucesso!", 2.5)
