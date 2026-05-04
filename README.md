-- SONER1X PRIVATE MACOS (TP STORE EDITION)
local Players = game:GetService("Players")
local RunService = game:GetService("RunService")
local UserInputService = game:GetService("UserInputService")
local Lighting = game:GetService("Lighting")
local LocalPlayer = Players.LocalPlayer
local Camera = workspace.CurrentCamera

local Settings = {
    Fly = false, FlySpeed = 60, AimLock = false, Chams = false,
    IsDay = true, Aspect = 1.777, V_Stretch = 1, BaseFOV = 80,
    WalkSpeed = 16, Spin = false, SpinSpeed = 60,
    StoredPos = nil -- Переменная для хранения точки телепорта
}

local ButtonStates = {}
local inputFlags = {forward = false, back = false, left = false, right = false, up = false, down = false}
local bv = Instance.new("BodyVelocity")
local bg = Instance.new("BodyGyro")
bv.MaxForce = Vector3.new(1e6, 1e6, 1e6)
bg.MaxTorque = Vector3.new(1e6, 1e6, 1e6)

local screenGui = Instance.new("ScreenGui", LocalPlayer:WaitForChild("PlayerGui"))
screenGui.Name = "SONER1X_TP_UI"
screenGui.ResetOnSpawn = false
screenGui.IgnoreGuiInset = true

local BG_COLOR = Color3.fromRGB(20, 20, 20)
local ACCENT_WHITE = Color3.fromRGB(255, 255, 255)

-- Функция перетаскивания
local function makeDraggable(gui, target)
    local dragging, dragStart, startPos
    gui.InputBegan:Connect(function(input)
        if input.UserInputType == Enum.UserInputType.MouseButton1 or input.UserInputType == Enum.UserInputType.Touch then
            dragging = true; dragStart = input.Position; startPos = target.Position
        end
    end)
    UserInputService.InputChanged:Connect(function(input)
        if dragging and (input.UserInputType == Enum.UserInputType.MouseMovement or input.UserInputType == Enum.UserInputType.Touch) then
            local delta = input.Position - dragStart
            target.Position = UDim2.new(startPos.X.Scale, startPos.X.Offset + delta.X, startPos.Y.Scale, startPos.Y.Offset + delta.Y)
        end
    end)
    UserInputService.InputEnded:Connect(function(input)
        if input.UserInputType == Enum.UserInputType.MouseButton1 or input.UserInputType == Enum.UserInputType.Touch then dragging = false end
    end)
end

-- Окна параметров (OPEN)
local function createParamBox(name, defaultVal)
    local f = Instance.new("Frame", screenGui)
    f.Size = UDim2.new(0, 150, 0, 100); f.Position = UDim2.new(0.7, 0, 0.4, 0)
    f.BackgroundColor3 = BG_COLOR; f.BackgroundTransparency = 0.1; f.Visible = false
    Instance.new("UICorner", f).CornerRadius = UDim.new(0, 15)
    Instance.new("UIStroke", f).Color = ACCENT_WHITE
    local t = Instance.new("TextLabel", f)
    t.Size = UDim2.new(1, 0, 0, 30); t.Text = name; t.TextColor3 = ACCENT_WHITE; t.BackgroundTransparency = 1; t.Font = "GothamBold"; t.TextSize = 12
    local v = Instance.new("TextLabel", f)
    v.Size = UDim2.new(0, 60, 0, 30); v.Position = UDim2.new(0.3, 0, 0.4, 0); v.Text = tostring(defaultVal); v.TextColor3 = ACCENT_WHITE; v.BackgroundTransparency = 1; v.TextSize = 18
    local m = Instance.new("TextButton", f); m.Size = UDim2.new(0, 30, 0, 30); m.Position = UDim2.new(0.1, 0, 0.4, 0); m.Text = "-"; m.BackgroundColor3 = ACCENT_WHITE; m.TextColor3 = Color3.new(0,0,0); Instance.new("UICorner", m)
    local p = Instance.new("TextButton", f); p.Size = UDim2.new(0, 30, 0, 30); p.Position = UDim2.new(0.7, 0, 0.4, 0); p.Text = "+"; p.BackgroundColor3 = ACCENT_WHITE; p.TextColor3 = Color3.new(0,0,0); Instance.new("UICorner", p)
    makeDraggable(f, f)
    return f, m, p, v
end

local sF, sM, sP, sV = createParamBox("WALK SPEED", Settings.WalkSpeed)
local spF, spM, spP, spV = createParamBox("SPIN SPEED", Settings.SpinSpeed)
local fF, fM, fP, fV = createParamBox("FOV SET", Settings.BaseFOV)

sP.MouseButton1Click:Connect(function() Settings.WalkSpeed += 5 sV.Text = tostring(Settings.WalkSpeed) end)
sM.MouseButton1Click:Connect(function() Settings.WalkSpeed = math.max(0, Settings.WalkSpeed - 5) sV.Text = tostring(Settings.WalkSpeed) end)
spP.MouseButton1Click:Connect(function() Settings.SpinSpeed += 10 spV.Text = tostring(Settings.SpinSpeed) end)
spM.MouseButton1Click:Connect(function() Settings.SpinSpeed -= 10 spV.Text = tostring(Settings.SpinSpeed) end)
fP.MouseButton1Click:Connect(function() Settings.BaseFOV += 5 fV.Text = tostring(Settings.BaseFOV) end)
fM.MouseButton1Click:Connect(function() Settings.BaseFOV = math.max(10, Settings.BaseFOV - 5) fV.Text = tostring(Settings.BaseFOV) end)

local appleBtn = Instance.new("TextButton", screenGui)
appleBtn.Size = UDim2.new(0, 50, 0, 50); appleBtn.Position = UDim2.new(0.05, 0, 0.1, 0); appleBtn.Text = ""
appleBtn.BackgroundColor3 = BG_COLOR; appleBtn.BackgroundTransparency = 0.5; appleBtn.TextColor3 = ACCENT_WHITE; appleBtn.TextSize = 30
Instance.new("UICorner", appleBtn).CornerRadius = UDim.new(0, 12); Instance.new("UIStroke", appleBtn).Color = ACCENT_WHITE
makeDraggable(appleBtn, appleBtn)

local main = Instance.new("Frame", screenGui)
main.Size = UDim2.new(0, 230, 0, 520); main.Position = UDim2.new(0.5, -115, 0.15, 0) -- Чуть увеличил высоту
main.BackgroundColor3 = BG_COLOR; main.BackgroundTransparency = 0.2; main.Visible = true
Instance.new("UICorner", main).CornerRadius = UDim.new(0, 16)
Instance.new("UIStroke", main).Color = ACCENT_WHITE

local titleBar = Instance.new("Frame", main); titleBar.Size = UDim2.new(1, 0, 0, 35); titleBar.BackgroundTransparency = 1
makeDraggable(titleBar, main)

local header = Instance.new("TextLabel", titleBar); header.Size = UDim2.new(1, 0, 1, 0); header.Text = "SONER1X PRIVATE"
header.Font = "GothamBold"; header.TextSize = 13; header.TextColor3 = ACCENT_WHITE; header.BackgroundTransparency = 1

appleBtn.MouseButton1Click:Connect(function() main.Visible = not main.Visible end)

-- Стандартная кнопка (Toggle)
local function btn(id, name, pos, cb)
    ButtonStates[id] = false
    local b = Instance.new("TextButton", main); b.Size = UDim2.new(0, 200, 0, 35); b.Position = UDim2.new(0, 15, 0, pos)
    b.Text = name; b.Font = "GothamMedium"; b.TextSize = 12; b.TextColor3 = ACCENT_WHITE; b.BackgroundColor3 = ACCENT_WHITE; b.BackgroundTransparency = 0.9; b.AutoButtonColor = false
    Instance.new("UICorner", b).CornerRadius = UDim.new(0, 8)
    b.MouseButton1Click:Connect(function()
        ButtonStates[id] = not ButtonStates[id]; cb(ButtonStates[id])
        if ButtonStates[id] then b.BackgroundTransparency = 0; b.TextColor3 = Color3.new(0,0,0) else b.BackgroundTransparency = 0.9; b.TextColor3 = ACCENT_WHITE end
    end)
end

-- Двойная кнопка (Action + OPEN)
local function dualBtn(id, name, pos, actionCb, setCb)
    ButtonStates[id] = false
    local container = Instance.new("Frame", main); container.Size = UDim2.new(0, 200, 0, 35); container.Position = UDim2.new(0, 15, 0, pos); container.BackgroundTransparency = 1
    local bLeft = Instance.new("TextButton", container); bLeft.Size = UDim2.new(0.65, -2, 1, 0); bLeft.Text = name
    bLeft.Font = "GothamMedium"; bLeft.TextSize = 12; bLeft.BackgroundColor3 = ACCENT_WHITE; bLeft.BackgroundTransparency = 0.9; bLeft.TextColor3 = ACCENT_WHITE
    Instance.new("UICorner", bLeft).CornerRadius = UDim.new(0, 8)
    local bRight = Instance.new("TextButton", container); bRight.Size = UDim2.new(0.35, -2, 1, 0); bRight.Position = UDim2.new(0.65, 2, 0, 0); bRight.Text = "OPEN"
    bRight.Font = "GothamBold"; bRight.TextSize = 10; bRight.BackgroundColor3 = ACCENT_WHITE; bRight.BackgroundTransparency = 0.8; bRight.TextColor3 = ACCENT_WHITE
    Instance.new("UICorner", bRight).CornerRadius = UDim.new(0, 8)
    bLeft.MouseButton1Click:Connect(function()
        ButtonStates[id] = not ButtonStates[id]; actionCb(ButtonStates[id])
        if ButtonStates[id] then bLeft.BackgroundTransparency = 0; bLeft.TextColor3 = Color3.new(0,0,0) else bLeft.BackgroundTransparency = 0.9; bLeft.TextColor3 = ACCENT_WHITE end
    end)
    bRight.MouseButton1Click:Connect(function()
        setCb(); bRight.BackgroundTransparency = 0; bRight.TextColor3 = Color3.new(0,0,0); task.wait(0.1); bRight.BackgroundTransparency = 0.8; bRight.TextColor3 = ACCENT_WHITE
    end)
end

-- Кнопки в меню
btn("fly", "Fly Mode", 50, function(val) Settings.Fly = val end)
dualBtn("speed", "WalkSpeed", 95, function(val) end, function() sF.Visible = not sF.Visible; spF.Visible = false; fF.Visible = false end)
dualBtn("spin", "Spin Hack", 140, function(val) Settings.Spin = val end, function() spF.Visible = not spF.Visible; sF.Visible = false; fF.Visible = false end)
btn("aim", "AimLock Toggle", 185, function(val) Settings.AimLock = val end)
btn("chams", "Visuals (Chams)", 230, function(val) Settings.Chams = val end)
dualBtn("fov", "FOV Change", 275, function(val) Settings.BaseFOV = val and 120 or 80 fV.Text = tostring(Settings.BaseFOV) end, function() fF.Visible = not fF.Visible; sF.Visible = false; spF.Visible = false end)
btn("res", "4:3 Resolution", 320, function(val) Settings.Aspect = val and 1.333 or 1.777; Settings.V_Stretch = val and 0.8 or 1 end)
btn("time", "Night Mode", 365, function(val) Settings.IsDay = not val end)

-- НОВАЯ КНОПКА: Teleport Store (TP | SET)
local tpFrame = Instance.new("Frame", main)
tpFrame.Size = UDim2.new(0, 200, 0, 35); tpFrame.Position = UDim2.new(0, 15, 0, 410); tpFrame.BackgroundTransparency = 1

local tpBtn = Instance.new("TextButton", tpFrame)
tpBtn.Size = UDim2.new(0.5, -2, 1, 0); tpBtn.Text = "TELEPORT"; tpBtn.Font = "GothamMedium"; tpBtn.TextSize = 11
tpBtn.BackgroundColor3 = ACCENT_WHITE; tpBtn.BackgroundTransparency = 0.9; tpBtn.TextColor3 = ACCENT_WHITE
Instance.new("UICorner", tpBtn).CornerRadius = UDim.new(0, 8)

local setBtn = Instance.new("TextButton", tpFrame)
setBtn.Size = UDim2.new(0.5, -2, 1, 0); setBtn.Position = UDim2.new(0.5, 2, 0, 0); setBtn.Text = "SET POS"
setBtn.Font = "GothamBold"; setBtn.TextSize = 11; setBtn.BackgroundColor3 = ACCENT_WHITE; setBtn.BackgroundTransparency = 0.8; setBtn.TextColor3 = ACCENT_WHITE
Instance.new("UICorner", setBtn).CornerRadius = UDim.new(0, 8)

-- Логика SET: Запоминаем позицию
setBtn.MouseButton1Click:Connect(function()
    local char = LocalPlayer.Character
    local hrp = char and char:FindFirstChild("HumanoidRootPart")
    if hrp then
        Settings.StoredPos = hrp.CFrame
        -- Визуальный отклик (мигание)
        setBtn.BackgroundTransparency = 0; setBtn.TextColor3 = Color3.new(0,0,0)
        task.wait(0.2)
        setBtn.BackgroundTransparency = 0.8; setBtn.TextColor3 = ACCENT_WHITE
    end
end)

-- Логика TP: Перемещаемся
tpBtn.MouseButton1Click:Connect(function()
    local char = LocalPlayer.Character
    local hrp = char and char:FindFirstChild("HumanoidRootPart")
    if hrp and Settings.StoredPos then
        hrp.CFrame = Settings.StoredPos
        -- Визуальный отклик
        tpBtn.BackgroundTransparency = 0; tpBtn.TextColor3 = Color3.new(0,0,0)
        task.wait(0.2)
        tpBtn.BackgroundTransparency = 0.9; tpBtn.TextColor3 = ACCENT_WHITE
    end
end)

-- Fly кнопки управления
local flyFrame = Instance.new("Frame", screenGui); flyFrame.Size = UDim2.new(1, 0, 1, 0); flyFrame.BackgroundTransparency = 1; flyFrame.Visible = false
local function fbtn(id, t, p)
    local b = Instance.new("TextButton", flyFrame); b.Size = UDim2.new(0, 75, 0, 75); b.Position = p; b.Text = t; b.BackgroundColor3 = Color3.new(0,0,0)
    b.BackgroundTransparency = 0.7; b.TextColor3 = ACCENT_WHITE; b.TextSize = 25; Instance.new("UICorner", b).CornerRadius = UDim.new(0, 20); Instance.new("UIStroke", b).Color = ACCENT_WHITE
    b.InputBegan:Connect(function(i) if i.UserInputType == Enum.UserInputType.Touch or i.UserInputType == Enum.UserInputType.MouseButton1 then inputFlags[id] = true; b.BackgroundTransparency = 0; b.TextColor3 = Color3.new(0,0,0) end end)
    b.InputEnded:Connect(function(i) if i.UserInputType == Enum.UserInputType.Touch or i.UserInputType == Enum.UserInputType.MouseButton1 then inputFlags[id] = false; b.BackgroundTransparency = 0.7; b.TextColor3 = ACCENT_WHITE end end)
end
fbtn("forward", "↑", UDim2.new(0, 110, 1, -240)); fbtn("back", "↓", UDim2.new(0, 110, 1, -80)); fbtn("left", "←", UDim2.new(0, 30, 1, -160)); fbtn("right", "→", UDim2.new(0, 190, 1, -160)); fbtn("up", "UP", UDim2.new(1, -110, 1, -240)); fbtn("down", "DN", UDim2.new(1, -110, 1, -80))

-- Цикл
RunService.RenderStepped:Connect(function()
    local char = LocalPlayer.Character; if not char then return end
    local hrp = char:FindFirstChild("HumanoidRootPart"); if not hrp then return end
    local hum = char:FindFirstChildOfClass("Humanoid"); if not hum then return end
    hum.WalkSpeed = Settings.WalkSpeed
    if Settings.Spin then hrp.CFrame = hrp.CFrame * CFrame.Angles(0, math.rad(Settings.SpinSpeed/10), 0) end
    flyFrame.Visible = Settings.Fly
    if Settings.Fly then
        bv.Parent = hrp; bg.Parent = hrp; bg.CFrame = Camera.CFrame
        local mVec = Vector3.new(0,0,0)
        if inputFlags.forward then mVec = mVec + Camera.CFrame.LookVector end
        if inputFlags.back then mVec = mVec - Camera.CFrame.LookVector end
        if inputFlags.left then mVec = mVec - Camera.CFrame.RightVector end
        if inputFlags.right then mVec = mVec + Camera.CFrame.RightVector end
        if inputFlags.up then mVec = mVec + Vector3.new(0,1,0) end
        if inputFlags.down then mVec = mVec - Vector3.new(0,1,0) end
        bv.Velocity = mVec * Settings.FlySpeed; hum.PlatformStand = true
    else bv.Parent = nil; bg.Parent = nil; hum.PlatformStand = false end
    if Settings.AimLock then
        local target = nil; local mDist = 500
        for _, p in pairs(Players:GetPlayers()) do
            if p ~= LocalPlayer and p.Character and p.Character:FindFirstChild("Head") then
                local pos, vis = Camera:WorldToViewportPoint(p.Character.Head.Position)
                if vis then
                    local mag = (Vector2.new(pos.X, pos.Y) - Vector2.new(Camera.ViewportSize.X/2, Camera.ViewportSize.Y/2)).Magnitude
                    if mag < mDist then mDist = mag; target = p.Character.Head end
                end
            end
        end
        if target then Camera.CFrame = CFrame.new(Camera.CFrame.Position, target.Position) end
    end
    for _, p in pairs(Players:GetPlayers()) do
        if p ~= LocalPlayer and p.Character then
            local h = p.Character:FindFirstChild("Highlight")
            if Settings.Chams then if not h then h = Instance.new("Highlight", p.Character) h.FillColor = Color3.new(1,0,0) end elseif h then h:Destroy() end
        end
    end
    Camera.FieldOfView = (Settings.BaseFOV * (Settings.Aspect / 1.777)) * Settings.V_Stretch
    Lighting.ClockTime = Settings.IsDay and 14 or 0
end)
