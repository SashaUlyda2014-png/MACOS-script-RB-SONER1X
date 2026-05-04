local Players = game:GetService("Players")
local RunService = game:GetService("RunService")
local UserInputService = game:GetService("UserInputService")
local Lighting = game:GetService("Lighting")
local LocalPlayer = Players.LocalPlayer
local Camera = workspace.CurrentCamera

local KEY_SYSTEM = "67GAZAN22867HYRASTENDOFF2"

local Settings = {
    Fly = false, FlySpeed = 60, AimLock = false, Chams = false,
    IsDay = true, WalkSpeed = 16, Spin = false, SpinSpeed = 60,
    StoredPos = nil
}

local sg = Instance.new("ScreenGui", LocalPlayer:WaitForChild("PlayerGui"))
sg.Name = "Soner1x_ULTIMATE"
sg.ResetOnSpawn = false

local function makeDraggable(obj)
    local dragging, dragStart, startPos
    obj.InputBegan:Connect(function(input)
        if input.UserInputType == Enum.UserInputType.MouseButton1 or input.UserInputType == Enum.UserInputType.Touch then
            dragging = true
            dragStart = input.Position
            startPos = obj.Position
        end
    end)
    UserInputService.InputChanged:Connect(function(input)
        if dragging and (input.UserInputType == Enum.UserInputType.MouseMovement or input.UserInputType == Enum.UserInputType.Touch) then
            local delta = input.Position - dragStart
            obj.Position = UDim2.new(startPos.X.Scale, startPos.X.Offset + delta.X, startPos.Y.Scale, startPos.Y.Offset + delta.Y)
        end
    end)
    UserInputService.InputEnded:Connect(function() dragging = false end)
end

local function StartSonix()
    local inputFlags = {forward = false, back = false, left = false, right = false, up = false, down = false}
    local bv = Instance.new("BodyVelocity")
    local bg = Instance.new("BodyGyro")
    bv.MaxForce = Vector3.new(1e6, 1e6, 1e6)
    bg.MaxTorque = Vector3.new(1e6, 1e6, 1e6)

    -- СВЕТЛАЯ КНОПКА APPLE
    local AppleBtn = Instance.new("TextButton", sg)
    AppleBtn.Size = UDim2.new(0, 60, 0, 60)
    AppleBtn.Position = UDim2.new(0.05, 0, 0.1, 0)
    AppleBtn.Text = ""
    AppleBtn.BackgroundColor3 = Color3.fromRGB(255, 255, 255)
    AppleBtn.BackgroundTransparency = 0.2
    AppleBtn.TextColor3 = Color3.fromRGB(0, 0, 0)
    AppleBtn.TextSize = 35
    Instance.new("UICorner", AppleBtn).CornerRadius = UDim.new(0, 18)
    local StrokeA = Instance.new("UIStroke", AppleBtn)
    StrokeA.Color = Color3.fromRGB(200, 200, 200)
    StrokeA.Thickness = 1.5
    makeDraggable(AppleBtn)

    -- ГЛАВНОЕ МЕНЮ (С твоим ником)
    local Main = Instance.new("Frame", sg)
    Main.Size = UDim2.new(0, 400, 0, 400)
    Main.Position = UDim2.new(0.5, -200, 0.5, -200)
    Main.BackgroundColor3 = Color3.fromRGB(255, 255, 255)
    Main.BackgroundTransparency = 0.15
    Main.Visible = true
    Instance.new("UICorner", Main).CornerRadius = UDim.new(0, 24)
    local StrokeM = Instance.new("UIStroke", Main)
    StrokeM.Color = Color3.fromRGB(220, 220, 220)
    StrokeM.Thickness = 2
    makeDraggable(Main)

    AppleBtn.MouseButton1Click:Connect(function()
        Main.Visible = not Main.Visible
    end)

    local Title = Instance.new("TextLabel", Main)
    Title.Size = UDim2.new(1, 0, 0, 50)
    Title.Text = "Soner1x PREMIER"
    Title.TextColor3 = Color3.fromRGB(40, 40, 40)
    Title.BackgroundTransparency = 1
    Title.Font = Enum.Font.GothamBold
    Title.TextSize = 18

    local Scroll = Instance.new("ScrollingFrame", Main)
    Scroll.Size = UDim2.new(1, -30, 1, -70)
    Scroll.Position = UDim2.new(0, 15, 0, 55)
    Scroll.BackgroundTransparency = 1
    Scroll.CanvasSize = UDim2.new(0, 0, 1.8, 0)
    Scroll.ScrollBarThickness = 0

    local function createSlider(name, val, cb)
        local f = Instance.new("Frame", sg)
        f.Size = UDim2.new(0, 170, 0, 100)
        f.Position = UDim2.new(0.7, 0, 0.4, 0)
        f.BackgroundColor3 = Color3.fromRGB(255, 255, 255)
        f.BackgroundTransparency = 0.1
        f.Visible = false
        Instance.new("UICorner", f).CornerRadius = UDim.new(0, 15)
        Instance.new("UIStroke", f).Color = Color3.fromRGB(200, 200, 200)
        makeDraggable(f)
        local t = Instance.new("TextLabel", f)
        t.Size = UDim2.new(1,0,0,30); t.Text = name; t.TextColor3 = Color3.new(0,0,0); t.BackgroundTransparency = 1
        local v = Instance.new("TextLabel", f)
        v.Size = UDim2.new(1,0,0,30); v.Position = UDim2.new(0,0,0.3,0)
        v.Text = tostring(val); v.TextColor3 = Color3.fromRGB(100, 100, 100); v.BackgroundTransparency = 1
        local m = Instance.new("TextButton", f)
        m.Size = UDim2.new(0,40,0,40); m.Position = UDim2.new(0.1,0,0.55,0); m.Text = "-"
        m.BackgroundColor3 = Color3.fromRGB(240,240,240); m.TextColor3 = Color3.new(0,0,0)
        Instance.new("UICorner", m)
        local p = Instance.new("TextButton", f)
        p.Size = UDim2.new(0,40,0,40); p.Position = UDim2.new(0.65,0,0.55,0); p.Text = "+"
        p.BackgroundColor3 = Color3.fromRGB(240,240,240); p.TextColor3 = Color3.new(0,0,0)
        Instance.new("UICorner", p)
        m.MouseButton1Click:Connect(function() v.Text = cb(-5) end)
        p.MouseButton1Click:Connect(function() v.Text = cb(5) end)
        return f
    end

    local speedS = createSlider("WALK SPEED", Settings.WalkSpeed, function(d) Settings.WalkSpeed = math.clamp(Settings.WalkSpeed+d, 0, 300) return Settings.WalkSpeed end)
    local spinS = createSlider("SPIN POWER", Settings.SpinSpeed, function(d) Settings.SpinSpeed = math.clamp(Settings.SpinSpeed+d, 0, 1000) return Settings.SpinSpeed end)

    local function addRow(name, y, cb, extra)
        local b = Instance.new("TextButton", Scroll)
        b.Size = UDim2.new(0, 280, 0, 40)
        b.Position = UDim2.new(0, 5, 0, y)
        b.Text = name; b.BackgroundColor3 = Color3.fromRGB(240, 240, 240); b.TextColor3 = Color3.fromRGB(50, 50, 50)
        Instance.new("UICorner", b).CornerRadius = UDim.new(0, 10)
        local active = false
        b.MouseButton1Click:Connect(function()
            active = not active
            b.BackgroundColor3 = active and Color3.fromRGB(255, 255, 255) or Color3.fromRGB(240, 240, 240)
            b.TextColor3 = active and Color3.fromRGB(0, 0, 0) or Color3.fromRGB(50, 50, 50)
            if active then Instance.new("UIStroke", b).Color = Color3.fromRGB(0, 0, 0) else
                local s = b:FindFirstChildOfClass("UIStroke") if s then s:Destroy() end
            end
            cb(active)
        end)
        if extra then
            local e = Instance.new("TextButton", Scroll)
            e.Size = UDim2.new(0, 60, 0, 40); e.Position = UDim2.new(0, 295, 0, y)
            e.Text = "SET"; e.BackgroundColor3 = Color3.fromRGB(220, 220, 220); e.TextColor3 = Color3.new(0, 0, 0)
            Instance.new("UICorner", e).CornerRadius = UDim.new(0, 10); e.MouseButton1Click:Connect(extra)
        end
    end

    addRow("Fly Hack", 0, function(v) Settings.Fly = v end)
    addRow("Walk Speed", 50, function() end, function() speedS.Visible = not speedS.Visible end)
    addRow("Spin Bot", 100, function(v) Settings.Spin = v end, function() spinS.Visible = not spinS.Visible end)
    addRow("Aim Assist", 150, function(v) Settings.AimLock = v end)
    addRow("ESP Chams", 200, function(v) Settings.Chams = v end)
    addRow("Force Night", 250, function(v) Settings.IsDay = not v end)

    addRow("Set Location", 310, function() 
        if LocalPlayer.Character and LocalPlayer.Character:FindFirstChild("HumanoidRootPart") then
            Settings.StoredPos = LocalPlayer.Character.HumanoidRootPart.CFrame
        end
    end)
    addRow("TP to Saved", 360, function() 
        if Settings.StoredPos and LocalPlayer.Character then
            LocalPlayer.Character.HumanoidRootPart.CFrame = Settings.StoredPos
        end
    end)
    addRow("TP to Player", 410, function() 
        local pList = Players:GetPlayers()
        local randomP = pList[math.random(1, #pList)]
        if randomP ~= LocalPlayer and randomP.Character then
            LocalPlayer.Character.HumanoidRootPart.CFrame = randomP.Character.HumanoidRootPart.CFrame
        end
    end)

    -- КНОПКИ ПОЛЕТА
    local fUI = Instance.new("Frame", sg); fUI.Size = UDim2.new(1,0,1,0); fUI.BackgroundTransparency = 1; fUI.Visible = false
    local function fb(id, t, p)
        local b = Instance.new("TextButton", fUI); b.Size = UDim2.new(0,65,0,65); b.Position = p; b.Text = t
        b.BackgroundColor3 = Color3.new(1, 1, 1); b.BackgroundTransparency = 0.3; b.TextColor3 = Color3.new(0, 0, 0)
        Instance.new("UICorner", b).CornerRadius = UDim.new(0, 15)
        Instance.new("UIStroke", b).Color = Color3.fromRGB(200, 200, 200)
        b.MouseButton1Down:Connect(function() inputFlags[id] = true end)
        b.MouseButton1Up:Connect(function() inputFlags[id] = false end)
    end
    
    fb("forward", "UP", UDim2.new(0.08, 0, 0.65, 0))
    fb("back", "DOWN", UDim2.new(0.08, 0, 0.8, 0))
    fb("left", "LEFT", UDim2.new(0.01, 0, 0.725, 0))
    fb("right", "RIGHT", UDim2.new(0.15, 0, 0.725, 0))
    fb("up", "FLY +", UDim2.new(0.85, 0, 0.65, 0))
    fb("down", "FLY -", UDim2.new(0.85, 0, 0.8, 0))

    RunService.RenderStepped:Connect(function()
        local char = LocalPlayer.Character; if not char then return end
        local hrp = char:FindFirstChild("HumanoidRootPart"); local hum = char:FindFirstChildOfClass("Humanoid")
        if not (hrp and hum) then return end

        hum.WalkSpeed = Settings.WalkSpeed
        if Settings.Spin then hrp.CFrame = hrp.CFrame * CFrame.Angles(0, math.rad(Settings.SpinSpeed/10), 0) end
        
        fUI.Visible = Settings.Fly
        if Settings.Fly then
            bv.Parent = hrp; bg.Parent = hrp; bg.CFrame = Camera.CFrame
            local moveDir = Vector3.new(0,0,0)
            if inputFlags.forward then moveDir = moveDir + Camera.CFrame.LookVector end
            if inputFlags.back then moveDir = moveDir - Camera.CFrame.LookVector end
            if inputFlags.left then moveDir = moveDir - Camera.CFrame.RightVector end
            if inputFlags.right then moveDir = moveDir + Camera.CFrame.RightVector end
            if inputFlags.up then moveDir = moveDir + Vector3.new(0,1,0) end
            if inputFlags.down then moveDir = moveDir - Vector3.new(0,1,0) end
            bv.Velocity = moveDir * Settings.FlySpeed; hum.PlatformStand = true
        else
            bv.Parent = nil; bg.Parent = nil; hum.PlatformStand = false
        end

        if Settings.AimLock then
            local target = nil; local dist = 500
            for _, p in pairs(Players:GetPlayers()) do
                if p ~= LocalPlayer and p.Character and p.Character:FindFirstChild("Head") then
                    local pPos, onScreen = Camera:WorldToViewportPoint(p.Character.Head.Position)
                    if onScreen then
                        local mag = (Vector2.new(pPos.X, pPos.Y) - Vector2.new(Camera.ViewportSize.X/2, Camera.ViewportSize.Y/2)).Magnitude
                        if mag < dist then dist = mag; target = p.Character.Head end
                    end
                end
            end
            if target then Camera.CFrame = CFrame.new(Camera.CFrame.Position, target.Position) end
        end

        for _, p in pairs(Players:GetPlayers()) do
            if p ~= LocalPlayer and p.Character then
                local h = p.Character:FindFirstChild("Highlight")
                if Settings.Chams then
                    if not h then h = Instance.new("Highlight", p.Character); h.FillColor = Color3.fromRGB(255, 255, 255) end
                elseif h then h:Destroy() end
            end
        end
        Lighting.ClockTime = Settings.IsDay and 14 or 0
    end)
end

-- Лоадер (С твоим ником)
local Loader = Instance.new("Frame", sg)
Loader.Size = UDim2.new(0, 320, 0, 180); Loader.Position = UDim2.new(0.5, -160, 0.4, 0)
Loader.BackgroundColor3 = Color3.fromRGB(255, 255, 255); Instance.new("UICorner", Loader).CornerRadius = UDim.new(0, 20)
Instance.new("UIStroke", Loader).Color = Color3.fromRGB(200, 200, 200)
makeDraggable(Loader)

local LT = Instance.new("TextLabel", Loader)
LT.Size = UDim2.new(1, 0, 0, 40); LT.Text = "Soner1x LOGIN"; LT.TextColor3 = Color3.new(0,0,0); LT.BackgroundTransparency = 1

local LI = Instance.new("TextBox", Loader)
LI.Size = UDim2.new(0, 240, 0, 45); LI.Position = UDim2.new(0.5, -120, 0.3, 0)
LI.PlaceholderText = "KEY"; LI.BackgroundColor3 = Color3.fromRGB(245, 245, 245); LI.TextColor3 = Color3.new(0,0,0)
Instance.new("UICorner", LI)

local LB = Instance.new("TextButton", Loader)
LB.Size = UDim2.new(0, 140, 0, 45); LB.Position = UDim2.new(0.5, -70, 0.65, 0)
LB.Text = "ACTIVATE"; LB.BackgroundColor3 = Color3.fromRGB(0, 0, 0); LB.TextColor3 = Color3.new(1, 1, 1)
Instance.new("UICorner", LB)

LB.MouseButton1Click:Connect(function()
    if LI.Text == KEY_SYSTEM then Loader:Destroy(); StartSonix() else LB.Text = "WRONG" end
end)
