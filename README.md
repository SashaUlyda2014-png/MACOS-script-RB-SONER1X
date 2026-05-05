local Players = game:GetService("Players")
local RunService = game:GetService("RunService")
local UserInputService = game:GetService("UserInputService")
local Lighting = game:GetService("Lighting")
local TeleportService = game:GetService("TeleportService")
local HttpService = game:GetService("HttpService")

local LocalPlayer = Players.LocalPlayer
local Camera = workspace.CurrentCamera

-- Access Settings
local KEY_SYSTEM = "FREE_KEY35483272387438"
local KEY_LINK = "https://direct-link.net/5554630/6YQ8vYMDUDeR"
local SESSION_FILE = "soner1x_session.json"

local Settings = {
    WalkSpeed = 16, InfJump = false, NoClip = false, Spider = false, Bhop = false,
    Fly = false, FlySpeed = 50,
    FullBright = false, BoxESP = false, Chams = false, Hitbox = false, HitboxSize = 5,
    AimLock = false, Trigger = false, NoRecoil = false, Spin = false, SpinSpeed = 60,
    AntiAFK = true, StoredPos = nil
}

local OriginalLighting = {
    Ambient = Lighting.Ambient,
    Brightness = Lighting.Brightness,
    ClockTime = Lighting.ClockTime
}

local sg = Instance.new("ScreenGui", LocalPlayer:WaitForChild("PlayerGui"))
sg.Name = "Soner1x_Win11_V15"
sg.ResetOnSpawn = false

-- Dragging Logic
local function makeDraggable(obj)
    local dragging, dragStart, startPos
    obj.InputBegan:Connect(function(input)
        if input.UserInputType == Enum.UserInputType.MouseButton1 or input.UserInputType == Enum.UserInputType.Touch then
            dragging = true; dragStart = input.Position; startPos = obj.Position
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

-- Main Menu
local function StartSonix()
    local Main = Instance.new("Frame", sg)
    Main.Size = UDim2.new(0, 460, 0, 520); Main.Position = UDim2.new(0.5, -230, 0.5, -260)
    Main.BackgroundColor3 = Color3.fromRGB(245, 245, 245); Main.BackgroundTransparency = 0.1
    Main.BorderSizePixel = 0; Instance.new("UICorner", Main).CornerRadius = UDim.new(0, 15)
    local MainStroke = Instance.new("UIStroke", Main); MainStroke.Color = Color3.fromRGB(0, 120, 215); MainStroke.Thickness = 2
    makeDraggable(Main)

    -- Windows Icon Button
    local WinBtn = Instance.new("Frame", sg)
    WinBtn.Size = UDim2.new(0, 55, 0, 55); WinBtn.Position = UDim2.new(0.02, 0, 0.1, 0)
    WinBtn.BackgroundColor3 = Color3.fromRGB(0, 120, 215); Instance.new("UICorner", WinBtn)
    makeDraggable(WinBtn)

    local function createSquare(pos)
        local s = Instance.new("Frame", WinBtn)
        s.Size = UDim2.new(0, 18, 0, 18); s.Position = pos; s.BackgroundColor3 = Color3.new(1, 1, 1); s.BorderSizePixel = 0
    end
    createSquare(UDim2.new(0.14, 0, 0.14, 0))
    createSquare(UDim2.new(0.54, 0, 0.14, 0))
    createSquare(UDim2.new(0.14, 0, 0.54, 0))
    createSquare(UDim2.new(0.54, 0, 0.54, 0))

    local WinTrigger = Instance.new("TextButton", WinBtn)
    WinTrigger.Size = UDim2.new(1, 0, 1, 0); WinTrigger.BackgroundTransparency = 1; WinTrigger.Text = ""
    WinTrigger.MouseButton1Click:Connect(function() Main.Visible = not Main.Visible end)

    -- Navigation Bar (LARGE)
    local Nav = Instance.new("Frame", Main)
    Nav.Size = UDim2.new(1, 0, 0, 60); Nav.BackgroundTransparency = 0.95; Nav.BackgroundColor3 = Color3.new(0,0,0)
    
    local TabMain = Instance.new("TextButton", Nav)
    TabMain.Size = UDim2.new(0.5, 0, 1, 0); TabMain.Text = "MAIN"; TabMain.Font = Enum.Font.GothamBold; TabMain.TextSize = 20
    TabMain.BackgroundTransparency = 1; TabMain.TextColor3 = Color3.fromRGB(0, 103, 192)

    local TabMisc = Instance.new("TextButton", Nav)
    TabMisc.Size = UDim2.new(0.5, 0, 1, 0); TabMisc.Position = UDim2.new(0.5, 0, 0, 0)
    TabMisc.Text = "MISC / SERVER"; TabMisc.Font = Enum.Font.GothamBold; TabMisc.TextSize = 20
    TabMisc.BackgroundTransparency = 1; TabMisc.TextColor3 = Color3.fromRGB(120, 120, 120)

    local Indicator = Instance.new("Frame", Nav)
    Indicator.Size = UDim2.new(0.4, 0, 0, 4); Indicator.Position = UDim2.new(0.05, 0, 0.9, 0); Indicator.BackgroundColor3 = Color3.fromRGB(0, 120, 215)

    -- Scrolling Pages
    local PageMain = Instance.new("ScrollingFrame", Main)
    PageMain.Size = UDim2.new(1, -20, 1, -80); PageMain.Position = UDim2.new(0, 10, 0, 70)
    PageMain.BackgroundTransparency = 1; PageMain.CanvasSize = UDim2.new(0, 0, 2.5, 0); PageMain.ScrollBarThickness = 2

    local PageMisc = Instance.new("ScrollingFrame", Main)
    PageMisc.Size = UDim2.new(1, -20, 1, -80); PageMisc.Position = UDim2.new(0, 10, 0, 70)
    PageMisc.BackgroundTransparency = 1; PageMisc.CanvasSize = UDim2.new(0, 0, 2.5, 0); PageMisc.Visible = false; PageMisc.ScrollBarThickness = 2

    TabMain.MouseButton1Click:Connect(function()
        PageMain.Visible = true; PageMisc.Visible = false; Indicator:TweenPosition(UDim2.new(0.05, 0, 0.9, 0), "Out", "Quad", 0.25)
        TabMain.TextColor3 = Color3.fromRGB(0, 103, 192); TabMisc.TextColor3 = Color3.fromRGB(120, 120, 120)
    end)
    TabMisc.MouseButton1Click:Connect(function()
        PageMain.Visible = false; PageMisc.Visible = true; Indicator:TweenPosition(UDim2.new(0.55, 0, 0.9, 0), "Out", "Quad", 0.25)
        TabMisc.TextColor3 = Color3.fromRGB(0, 103, 192); TabMain.TextColor3 = Color3.fromRGB(120, 120, 120)
    end)

    -- SET Panel
    local SetMenu = Instance.new("Frame", sg)
    SetMenu.Size = UDim2.new(0, 200, 0, 130); SetMenu.Position = UDim2.new(0.5, 240, 0.5, -65)
    SetMenu.BackgroundColor3 = Color3.new(1, 1, 1); SetMenu.Visible = false; Instance.new("UICorner", SetMenu)
    Instance.new("UIStroke", SetMenu).Color = Color3.fromRGB(0, 120, 215)
    local SetTitle = Instance.new("TextLabel", SetMenu); SetTitle.Size = UDim2.new(1,0,0,30); SetTitle.Text = "ADJUST"; SetTitle.BackgroundTransparency = 1
    local SetVal = Instance.new("TextLabel", SetMenu); SetVal.Size = UDim2.new(1,0,0,40); SetVal.Position = UDim2.new(0,0,0.25,0); SetVal.TextSize = 20; SetVal.Text = "0"; SetVal.BackgroundTransparency = 1
    local Minus = Instance.new("TextButton", SetMenu); Minus.Size = UDim2.new(0,50,0,40); Minus.Position = UDim2.new(0.1,0,0.65,0); Minus.Text = "-"; Minus.BackgroundColor3 = Color3.fromRGB(240,240,240); Instance.new("UICorner", Minus)
    local Plus = Instance.new("TextButton", SetMenu); Plus.Size = UDim2.new(0,50,0,40); Plus.Position = UDim2.new(0.65,0,0.65,0); Plus.Text = "+"; Plus.BackgroundColor3 = Color3.fromRGB(0,120,215); Plus.TextColor3 = Color3.new(1,1,1); Instance.new("UICorner", Plus)

    local function openSet(name, current, step, cb)
        if SetMenu.Visible and SetTitle.Text == name then SetMenu.Visible = false return end
        SetMenu.Visible = true; SetTitle.Text = name; SetVal.Text = tostring(current)
        local c1, c2
        c1 = Minus.MouseButton1Click:Connect(function() current = current - step; SetVal.Text = tostring(current); cb(current) end)
        c2 = Plus.MouseButton1Click:Connect(function() current = current + step; SetVal.Text = tostring(current); cb(current) end)
        task.spawn(function() repeat task.wait() until not SetMenu.Visible; c1:Disconnect(); c2:Disconnect() end)
    end

    local function addRow(parent, name, y, key, setCb)
        local b = Instance.new("TextButton", parent)
        b.Size = UDim2.new(0, 320, 0, 40); b.Position = UDim2.new(0, 5, 0, y)
        b.Text = name; b.Font = Enum.Font.Gotham; b.BorderSizePixel = 0
        Instance.new("UICorner", b).CornerRadius = UDim.new(0, 6)
        
        local function refresh()
            b.BackgroundColor3 = Settings[key] and Color3.fromRGB(0, 120, 215) or Color3.fromRGB(255, 255, 255)
            b.TextColor3 = Settings[key] and Color3.new(1, 1, 1) or Color3.fromRGB(50, 50, 50)
        end
        refresh()

        b.MouseButton1Click:Connect(function()
            Settings[key] = not Settings[key]
            refresh()
            if key == "FullBright" and not Settings[key] then
                Lighting.Ambient = OriginalLighting.Ambient; Lighting.Brightness = OriginalLighting.Brightness; Lighting.ClockTime = OriginalLighting.ClockTime
            end
        end)

        if setCb then
            local s = Instance.new("TextButton", parent)
            s.Size = UDim2.new(0, 60, 0, 40); s.Position = UDim2.new(0, 335, 0, y); s.Text = "SET"
            s.BackgroundColor3 = Color3.fromRGB(235, 235, 235); Instance.new("UICorner", s)
            s.MouseButton1Click:Connect(setCb)
        end
    end

    -- MAIN TAB
    addRow(PageMain, "Fly Mode", 0, "Fly", function() openSet("FLY SPEED", Settings.FlySpeed, 10, function(v) Settings.FlySpeed = v end) end)
    addRow(PageMain, "WalkSpeed", 50, "dummy", function() openSet("SPEED", Settings.WalkSpeed, 5, function(v) Settings.WalkSpeed = v end) end)
    addRow(PageMain, "Infinity Jump", 100, "InfJump")
    addRow(PageMain, "No Clip", 150, "NoClip")
    addRow(PageMain, "Auto Bhop", 200, "Bhop")
    addRow(PageMain, "Aim Assist", 250, "AimLock")
    addRow(PageMain, "Hitbox Expander", 300, "Hitbox", function() openSet("HITBOX", Settings.HitboxSize, 2, function(v) Settings.HitboxSize = v end) end)

    -- MISC TAB
    addRow(PageMisc, "Full Brightness", 0, "FullBright")
    addRow(PageMisc, "Box ESP", 50, "BoxESP")
    addRow(PageMisc, "Chams (Glow)", 100, "Chams")
    addRow(PageMisc, "Spider Walk", 150, "Spider")
    addRow(PageMisc, "Spin Bot", 200, "Spin", function() openSet("SPIN", Settings.SpinSpeed, 20, function(v) Settings.SpinSpeed = v end) end)

    local function addBtn(parent, name, y, cb)
        local b = Instance.new("TextButton", parent); b.Size = UDim2.new(0, 410, 0, 40); b.Position = UDim2.new(0, 5, 0, y)
        b.Text = name; b.BackgroundColor3 = Color3.new(1,1,1); b.Font = Enum.Font.Gotham; Instance.new("UICorner", b)
        b.MouseButton1Click:Connect(cb)
    end

    addBtn(PageMisc, "SAVE POSITION", 260, function() if LocalPlayer.Character then Settings.StoredPos = LocalPlayer.Character.HumanoidRootPart.CFrame end end)
    addBtn(PageMisc, "TELEPORT TO SAVED", 310, function()
        if Settings.StoredPos and LocalPlayer.Character then
            LocalPlayer.Character.HumanoidRootPart.CFrame = Settings.StoredPos + Vector3.new(0,3,0)
            task.wait(0.05); LocalPlayer.Character.HumanoidRootPart.CFrame = Settings.StoredPos
        end
    end)
    addBtn(PageMisc, "REJOIN SERVER", 360, function() TeleportService:Teleport(game.PlaceId, LocalPlayer) end)

    -- Fly Logic
    RunService.RenderStepped:Connect(function()
        local char = LocalPlayer.Character
        local hrp = char and char:FindFirstChild("HumanoidRootPart")
        if Settings.Fly and hrp then
            local vel = Vector3.new(0, 0, 0)
            if UserInputService:IsKeyDown(Enum.KeyCode.W) then vel = vel + Camera.CFrame.LookVector end
            if UserInputService:IsKeyDown(Enum.KeyCode.S) then vel = vel - Camera.CFrame.LookVector end
            if UserInputService:IsKeyDown(Enum.KeyCode.A) then vel = vel - Camera.CFrame.RightVector end
            if UserInputService:IsKeyDown(Enum.KeyCode.D) then vel = vel + Camera.CFrame.RightVector end
            hrp.Velocity = vel * Settings.FlySpeed
            hrp.CFrame = CFrame.new(hrp.Position, hrp.Position + Camera.CFrame.LookVector)
        end
    end)

    -- Standard Loops
    RunService.Stepped:Connect(function()
        local char = LocalPlayer.Character; if not char then return end
        local hum = char:FindFirstChildOfClass("Humanoid")
        local hrp = char:FindFirstChild("HumanoidRootPart")
        if hum and not Settings.Fly then hum.WalkSpeed = Settings.WalkSpeed end
        if Settings.Bhop and hum and hum.MoveDirection.Magnitude > 0 and hum.FloorMaterial ~= Enum.Material.Air then hum:ChangeState(Enum.HumanoidStateType.Jumping) end
        if Settings.FullBright then Lighting.Ambient = Color3.new(1,1,1); Lighting.Brightness = 2; Lighting.ClockTime = 14 end
        if Settings.Spin and hrp then hrp.CFrame = hrp.CFrame * CFrame.Angles(0, math.rad(Settings.SpinSpeed/10), 0) end
        for _, v in pairs(char:GetDescendants()) do if v:IsA("BasePart") then v.CanCollide = not Settings.NoClip end end
    end)

    -- Inf Jump Fix
    UserInputService.JumpRequest:Connect(function()
        if Settings.InfJump then
            local char = LocalPlayer.Character
            if char and char:FindFirstChildOfClass("Humanoid") then
                char:FindFirstChildOfClass("Humanoid"):ChangeState(Enum.HumanoidStateType.Jumping)
            end
        end
    end)

    -- Visuals
    RunService.RenderStepped:Connect(function()
        for _, p in pairs(Players:GetPlayers()) do
            if p ~= LocalPlayer and p.Character then
                local h = p.Character:FindFirstChild("Head")
                if h then
                    if Settings.Hitbox then h.Size = Vector3.new(Settings.HitboxSize, Settings.HitboxSize, Settings.HitboxSize); h.Transparency = 0.6; h.CanCollide = false
                    else h.Size = Vector3.new(2, 1, 1); h.Transparency = 0 end
                end
                local ch = p.Character:FindFirstChildOfClass("Highlight")
                if Settings.Chams then if not ch then Instance.new("Highlight", p.Character) end elseif ch then ch:Destroy() end
                local b = p.Character:FindFirstChild("SonixBox")
                if Settings.BoxESP then
                    if not b then b = Instance.new("BoxHandleAdornment", p.Character); b.Name = "SonixBox"; b.Size = Vector3.new(4, 6, 0.2); b.AlwaysOnTop = true; b.Adornee = p.Character; b.Transparency = 0.5; b.Color3 = Color3.fromRGB(0, 120, 215) end
                elseif b then b:Destroy() end
            end
        end
        if Settings.AimLock then
            local target = nil; local maxDist = 250
            for _, p in pairs(Players:GetPlayers()) do
                if p ~= LocalPlayer and p.Character and p.Character:FindFirstChild("Head") then
                    local pos, onScreen = Camera:WorldToViewportPoint(p.Character.Head.Position)
                    if onScreen then
                        local dist = (Vector2.new(pos.X, pos.Y) - Vector2.new(Camera.ViewportSize.X/2, Camera.ViewportSize.Y/2)).Magnitude
                        if dist < maxDist then maxDist = dist; target = p.Character.Head end
                    end
                end
            end
            if target then Camera.CFrame = Camera.CFrame:Lerp(CFrame.new(Camera.CFrame.Position, target.Position), 0.15) end
        end
    end)
end

-- Session Handler
local function checkSession()
    if isfile and readfile and isfile(SESSION_FILE) then
        local data = HttpService:JSONDecode(readfile(SESSION_FILE))
        if os.time() - data.Time < 3600 then return true end
    end
    return false
end

local function saveSession()
    if writefile then
        local data = {Time = os.time()}
        writefile(SESSION_FILE, HttpService:JSONEncode(data))
    end
end

if checkSession() then
    StartSonix()
else
    local Loader = Instance.new("Frame", sg); Loader.Size = UDim2.new(0, 300, 0, 220); Loader.Position = UDim2.new(0.5, -150, 0.4, 0); Loader.BackgroundColor3 = Color3.new(1,1,1); Instance.new("UICorner", Loader); makeDraggable(Loader)
    local Title = Instance.new("TextLabel", Loader); Title.Size = UDim2.new(1,0,0,40); Title.Text = "SONER1X V15"; Title.Font = Enum.Font.GothamBold; Title.BackgroundTransparency = 1
    local LI = Instance.new("TextBox", Loader); LI.Size = UDim2.new(0, 240, 0, 40); LI.Position = UDim2.new(0.5, -120, 0.3, 0); LI.PlaceholderText = "ENTER KEY"; LI.BackgroundColor3 = Color3.fromRGB(240,240,240); Instance.new("UICorner", LI)
    local GK = Instance.new("TextButton", Loader); GK.Size = UDim2.new(0, 115, 0, 40); GK.Position = UDim2.new(0.5, -120, 0.65, 0); GK.Text = "GET KEY"; GK.BackgroundColor3 = Color3.fromRGB(230,230,230); Instance.new("UICorner", GK)
    local LB = Instance.new("TextButton", Loader); LB.Size = UDim2.new(0, 115, 0, 40); LB.Position = UDim2.new(0.5, 5, 0.65, 0); LB.Text = "LOGIN"; LB.BackgroundColor3 = Color3.fromRGB(0, 120, 215); LB.TextColor3 = Color3.new(1,1,1); Instance.new("UICorner", LB)
    
    GK.MouseButton1Click:Connect(function() if setclipboard then setclipboard(KEY_LINK) end end)
    LB.MouseButton1Click:Connect(function() if LI.Text == KEY_SYSTEM then saveSession(); Loader:Destroy(); StartSonix() end end)
end
