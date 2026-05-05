local Players = game:GetService("Players")
local RunService = game:GetService("RunService")
local UserInputService = game:GetService("UserInputService")
local Lighting = game:GetService("Lighting")
local ReplicatedStorage = game:GetService("ReplicatedStorage")
local TeleportService = game:GetService("TeleportService")
local HttpService = game:GetService("HttpService")

local LocalPlayer = Players.LocalPlayer
local Camera = workspace.CurrentCamera
local Mouse = LocalPlayer:GetMouse()

local KEY_SYSTEM = "FREE_KEY35483272387438"
local KEY_LINK = "https://direct-link.net/5554630/6YQ8vYMDUDeR"

local Settings = {
    Fly = false, FlySpeed = 60, AimLock = false, Chams = false, BoxESP = false,
    WalkSpeed = 16, Spin = false, SpinSpeed = 60,
    InfJump = false, Hitbox = false, HitboxSize = 5,
    FullBright = false, AntiAFK = true, NoClip = false,
    Bhop = false, Spider = false, Trigger = false, NoRecoil = false,
    StoredPos = nil
}

local OriginalLighting = {
    Ambient = Lighting.Ambient,
    OutdoorAmbient = Lighting.OutdoorAmbient,
    Brightness = Lighting.Brightness,
    ClockTime = Lighting.ClockTime
}

-- Anti-AFK
pcall(function()
    LocalPlayer.Idled:Connect(function()
        if Settings.AntiAFK then
            game:GetService("VirtualUser"):CaptureController()
            game:GetService("VirtualUser"):ClickButton2(Vector2.new())
        end
    end)
end)

local sg = Instance.new("ScreenGui", LocalPlayer:WaitForChild("PlayerGui"))
sg.Name = "Soner1x_HUB_V11"
sg.ResetOnSpawn = false

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

local function StartSonix()
    -- Главное окно
    local Main = Instance.new("Frame", sg)
    Main.Size = UDim2.new(0, 420, 0, 520); Main.Position = UDim2.new(0.5, -210, 0.5, -260)
    Main.BackgroundColor3 = Color3.fromRGB(15, 15, 15); Main.BackgroundTransparency = 0.05
    Main.Visible = true; Main.BorderSizePixel = 0
    Instance.new("UICorner", Main).CornerRadius = UDim.new(0, 15)
    makeDraggable(Main)

    -- Логотип PornHub Style
    local LogoContainer = Instance.new("Frame", Main)
    LogoContainer.Size = UDim2.new(1, 0, 0, 60); LogoContainer.BackgroundTransparency = 1
    
    local NameLabel = Instance.new("TextLabel", LogoContainer)
    NameLabel.Text = "Soner1x"; NameLabel.TextColor3 = Color3.new(1, 1, 1)
    NameLabel.Font = Enum.Font.GothamBold; NameLabel.TextSize = 24
    NameLabel.Size = UDim2.new(0, 110, 1, 0); NameLabel.Position = UDim2.new(0.2, 0, 0, 0); NameLabel.BackgroundTransparency = 1

    local HubLabel = Instance.new("TextLabel", LogoContainer)
    HubLabel.Text = "PREMIER"; HubLabel.TextColor3 = Color3.new(0, 0, 0)
    HubLabel.Font = Enum.Font.GothamBold; HubLabel.TextSize = 24; HubLabel.BackgroundColor3 = Color3.fromRGB(255, 153, 0)
    HubLabel.Size = UDim2.new(0, 120, 0, 35); HubLabel.Position = UDim2.new(0.48, 0, 0.2, 0)
    Instance.new("UICorner", HubLabel).CornerRadius = UDim.new(0, 5)

    local AppleBtn = Instance.new("TextButton", sg)
    AppleBtn.Size = UDim2.new(0, 60, 0, 60); AppleBtn.Position = UDim2.new(0.05, 0, 0.1, 0)
    AppleBtn.Text = ""; AppleBtn.BackgroundColor3 = Color3.fromRGB(255, 153, 0)
    AppleBtn.TextColor3 = Color3.new(0, 0, 0); AppleBtn.TextSize = 35; Instance.new("UICorner", AppleBtn)
    AppleBtn.MouseButton1Click:Connect(function() Main.Visible = not Main.Visible end)
    makeDraggable(AppleBtn)

    local Scroll = Instance.new("ScrollingFrame", Main)
    Scroll.Size = UDim2.new(1, -20, 1, -80); Scroll.Position = UDim2.new(0, 10, 0, 70)
    Scroll.BackgroundTransparency = 1; Scroll.CanvasSize = UDim2.new(0, 0, 6, 0); Scroll.ScrollBarThickness = 2
    Scroll.ScrollBarImageColor3 = Color3.fromRGB(255, 153, 0)

    -- Настройки (SET)
    local SetMenu = Instance.new("Frame", sg)
    SetMenu.Size = UDim2.new(0, 220, 0, 130); SetMenu.Position = UDim2.new(0.5, 100, 0.5, -65)
    SetMenu.BackgroundColor3 = Color3.fromRGB(25, 25, 25); SetMenu.Visible = false; Instance.new("UICorner", SetMenu)
    local SetStroke = Instance.new("UIStroke", SetMenu); SetStroke.Color = Color3.fromRGB(255, 153, 0); SetStroke.Thickness = 2
    makeDraggable(SetMenu)
    
    local SetTitle = Instance.new("TextLabel", SetMenu); SetTitle.Size = UDim2.new(1,0,0,30); SetTitle.Text = "ADJUST"; SetTitle.TextColor3 = Color3.new(1,1,1); SetTitle.BackgroundTransparency = 1
    local SetVal = Instance.new("TextLabel", SetMenu); SetVal.Size = UDim2.new(1,0,0,30); SetVal.Position = UDim2.new(0,0,0.3,0); SetVal.Text = "0"; SetVal.TextColor3 = Color3.fromRGB(255, 153, 0); SetVal.BackgroundTransparency = 1
    local Minus = Instance.new("TextButton", SetMenu); Minus.Size = UDim2.new(0,50,0,40); Minus.Position = UDim2.new(0.1,0,0.6,0); Minus.Text = "-"; Minus.BackgroundColor3 = Color3.fromRGB(40,40,40); Minus.TextColor3 = Color3.new(1,1,1); Instance.new("UICorner", Minus)
    local Plus = Instance.new("TextButton", SetMenu); Plus.Size = UDim2.new(0,50,0,40); Plus.Position = UDim2.new(0.65,0,0.6,0); Plus.Text = "+"; Plus.BackgroundColor3 = Color3.fromRGB(255, 153, 0); Plus.TextColor3 = Color3.new(0,0,0); Instance.new("UICorner", Plus)

    local currentSetConnection = nil
    local function openSet(name, current, step, cb)
        if SetMenu.Visible and SetTitle.Text == name then SetMenu.Visible = false return end
        if currentSetConnection then currentSetConnection:Disconnect() end
        SetMenu.Visible = true; SetTitle.Text = name; SetVal.Text = tostring(current)
        local c1 = Minus.MouseButton1Click:Connect(function() current = current - step; SetVal.Text = tostring(current); cb(current) end)
        local c2 = Plus.MouseButton1Click:Connect(function() current = current + step; SetVal.Text = tostring(current); cb(current) end)
        currentSetConnection = RunService.Heartbeat:Connect(function() if not SetMenu.Visible then c1:Disconnect(); c2:Disconnect(); currentSetConnection:Disconnect() end end)
    end

    local function addRow(name, y, key, setCb)
        local b = Instance.new("TextButton", Scroll)
        b.Size = UDim2.new(0, 290, 0, 40); b.Position = UDim2.new(0, 5, 0, y)
        b.Text = name; b.Font = Enum.Font.Gotham; b.TextColor3 = Color3.new(1,1,1)
        Instance.new("UICorner", b).CornerRadius = UDim.new(0, 8)
        
        local function refresh()
            b.BackgroundColor3 = Settings[key] and Color3.fromRGB(255, 153, 0) or Color3.fromRGB(40, 40, 40)
            b.TextColor3 = Settings[key] and Color3.new(0,0,0) or Color3.new(1,1,1)
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
            local s = Instance.new("TextButton", Scroll)
            s.Size = UDim2.new(0, 60, 0, 40); s.Position = UDim2.new(0, 305, 0, y); s.Text = "SET"
            s.BackgroundColor3 = Color3.fromRGB(60,60,60); s.TextColor3 = Color3.new(1,1,1); Instance.new("UICorner", s)
            s.MouseButton1Click:Connect(setCb)
        end
    end

    -- Функции
    addRow("WalkSpeed", 0, "dummy", function() openSet("SPEED", Settings.WalkSpeed, 5, function(v) Settings.WalkSpeed = v end) end)
    addRow("Infinity Jump", 50, "InfJump")
    addRow("No Clip", 100, "NoClip")
    addRow("Spider-Man", 150, "Spider")
    addRow("Auto Bhop", 200, "Bhop")
    addRow("Full Bright", 250, "FullBright")
    addRow("Box ESP", 300, "BoxESP")
    addRow("Chams ESP", 350, "Chams")
    addRow("Hitbox Expander", 400, "Hitbox", function() openSet("HITBOX", Settings.HitboxSize, 2, function(v) Settings.HitboxSize = v end) end)
    addRow("Aim Assist", 450, "AimLock")
    addRow("Trigger Bot", 500, "Trigger")
    addRow("No Recoil", 550, "NoRecoil")
    addRow("Spin Bot", 600, "Spin", function() openSet("SPIN", Settings.SpinSpeed, 20, function(v) Settings.SpinSpeed = v end) end)

    local function addBtn(name, y, cb)
        local b = Instance.new("TextButton", Scroll); b.Size = UDim2.new(0, 360, 0, 40); b.Position = UDim2.new(0, 5, 0, y)
        b.Text = name; b.BackgroundColor3 = Color3.fromRGB(50,50,50); b.TextColor3 = Color3.fromRGB(255, 153, 0); Instance.new("UICorner", b)
        b.MouseButton1Click:Connect(cb)
    end

    addBtn("SAVE POSITION", 660, function() if LocalPlayer.Character then Settings.StoredPos = LocalPlayer.Character.HumanoidRootPart.CFrame end end)
    addBtn("TP TO SAVED", 710, function()
        if Settings.StoredPos and LocalPlayer.Character then
            LocalPlayer.Character.HumanoidRootPart.CFrame = Settings.StoredPos + Vector3.new(0,3,0)
            task.wait(0.05)
            LocalPlayer.Character.HumanoidRootPart.CFrame = Settings.StoredPos
        end
    end)
    addBtn("REJOIN SERVER", 760, function() TeleportService:Teleport(game.PlaceId, LocalPlayer) end)
    
    local function findServer(isSmall)
        local url = "https://games.roblox.com/v1/games/"..game.PlaceId.."/servers/Public?sortOrder="..(isSmall and "Asc" or "Desc").."&limit=100"
        local servers = HttpService:JSONDecode(game:HttpGet(url)).data
        for _, s in pairs(servers) do if s.playing < s.maxPlayers and s.id ~= game.JobId then TeleportService:TeleportToPlaceInstance(game.PlaceId, s.id) break end end
    end
    addBtn("SMALL SERVER", 810, function() findServer(true) end)
    addBtn("BIG SERVER", 860, function() findServer(false) end)

    -- Aim Assist
    RunService.RenderStepped:Connect(function()
        if not Settings.AimLock then return end
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
    end)

    -- Main Loop
    RunService.Stepped:Connect(function()
        local char = LocalPlayer.Character; if not char then return end
        local hum = char:FindFirstChildOfClass("Humanoid")
        local hrp = char:FindFirstChild("HumanoidRootPart")
        if hum then hum.WalkSpeed = Settings.WalkSpeed end
        for _, v in pairs(char:GetDescendants()) do if v:IsA("BasePart") then v.CanCollide = not Settings.NoClip end end
        if Settings.Spider and hrp then
            local r = Ray.new(hrp.Position, hrp.CFrame.LookVector * 2.5)
            if workspace:FindPartOnRay(r, char) then hrp.Velocity = Vector3.new(hrp.Velocity.X, 35, hrp.Velocity.Z) end
        end
        if Settings.Bhop and hum and hum.MoveDirection.Magnitude > 0 and hum.FloorMaterial ~= Enum.Material.Air then hum:ChangeState(Enum.HumanoidStateType.Jumping) end
        if Settings.FullBright then Lighting.Ambient = Color3.new(1,1,1); Lighting.Brightness = 2; Lighting.ClockTime = 14 end
        if Settings.Spin and hrp then hrp.CFrame = hrp.CFrame * CFrame.Angles(0, math.rad(Settings.SpinSpeed/10), 0) end
    end)

    -- Visuals
    RunService.RenderStepped:Connect(function()
        for _, p in pairs(Players:GetPlayers()) do
            if p ~= LocalPlayer and p.Character then
                local h = p.Character:FindFirstChild("Head")
                if h then
                    if Settings.Hitbox then
                        h.Size = Vector3.new(Settings.HitboxSize, Settings.HitboxSize, Settings.HitboxSize)
                        h.Transparency = 0.6; h.CanCollide = false
                    else
                        h.Size = Vector3.new(2, 1, 1); h.Transparency = 0
                    end
                end
                local ch = p.Character:FindFirstChildOfClass("Highlight")
                if Settings.Chams then if not ch then Instance.new("Highlight", p.Character) end elseif ch then ch:Destroy() end
                
                local b = p.Character:FindFirstChild("SonixBox")
                if Settings.BoxESP then
                    if not b then
                        b = Instance.new("BoxHandleAdornment", p.Character); b.Name = "SonixBox"
                        b.Size = Vector3.new(4, 6, 0.2); b.AlwaysOnTop = true; b.Adornee = p.Character
                        b.Transparency = 0.5; b.Color3 = Color3.fromRGB(255, 153, 0)
                    end
                elseif b then b:Destroy() end
            end
        end
    end)

    -- Inf Jump
    UserInputService.JumpRequest:Connect(function()
        if Settings.InfJump then
            local hum = LocalPlayer.Character and LocalPlayer.Character:FindFirstChildOfClass("Humanoid")
            if hum then hum:ChangeState(Enum.HumanoidStateType.Jumping) end
        end
    end)
end

-- Loader
local Loader = Instance.new("Frame", sg); Loader.Size = UDim2.new(0, 320, 0, 240); Loader.Position = UDim2.new(0.5, -160, 0.4, 0); Loader.BackgroundColor3 = Color3.fromRGB(15, 15, 15); Instance.new("UICorner", Loader)
local LStroke = Instance.new("UIStroke", Loader); LStroke.Color = Color3.fromRGB(255, 153, 0); LStroke.Thickness = 2
makeDraggable(Loader)
local LI = Instance.new("TextBox", Loader); LI.Size = UDim2.new(0, 240, 0, 45); LI.Position = UDim2.new(0.5, -120, 0.2, 0); LI.PlaceholderText = "ENTER KEY"; LI.BackgroundColor3 = Color3.fromRGB(30, 30, 30); LI.TextColor3 = Color3.new(1,1,1); Instance.new("UICorner", LI)
local GK = Instance.new("TextButton", Loader); GK.Size = UDim2.new(0, 115, 0, 40); GK.Position = UDim2.new(0.5, -120, 0.5, 0); GK.Text = "GET KEY"; GK.BackgroundColor3 = Color3.fromRGB(40, 40, 40); GK.TextColor3 = Color3.new(1,1,1); Instance.new("UICorner", GK)
local LB = Instance.new("TextButton", Loader); LB.Size = UDim2.new(0, 115, 0, 40); LB.Position = UDim2.new(0.5, 5, 0.5, 0); LB.Text = "LOGIN"; LB.BackgroundColor3 = Color3.fromRGB(255, 153, 0); LB.TextColor3 = Color3.new(0,0,0); Instance.new("UICorner", LB)
GK.MouseButton1Click:Connect(function() if setclipboard then setclipboard(KEY_LINK) end end)
LB.MouseButton1Click:Connect(function() if LI.Text == KEY_SYSTEM then Loader:Destroy(); StartSonix() end end)
