local Players = game:GetService("Players")
local RunService = game:GetService("RunService")
local UserInputService = game:GetService("UserInputService")
local Lighting = game:GetService("Lighting")
local HttpService = game:GetService("HttpService")
local TeleportService = game:GetService("TeleportService")

local LocalPlayer = Players.LocalPlayer
local Camera = workspace.CurrentCamera

-- Key System
local KEY_SYSTEM = "FREE_KEY35483272387438"
local SESSION_FILE = "soner1x_session.json"

local Settings = {
    WalkSpeed = 16, InfJump = false, NoClip = false, Spider = false, Bhop = false,
    Fly = false, FlySpeed = 50,
    FullBright = false, BoxESP = false, Chams = false, Hitbox = false, HitboxSize = 5,
    AimLock = false
}

local OriginalLight = {
    Brightness = Lighting.Brightness,
    ClockTime = Lighting.ClockTime,
    Ambient = Lighting.Ambient
}

local sg = Instance.new("ScreenGui", LocalPlayer:WaitForChild("PlayerGui"))
sg.Name = "Soner1x_Ultimate_V21"
sg.ResetOnSpawn = false

-- Draggable Logic
local function makeDraggable(gui, handle)
    local dragging, dragInput, dragStart, startPos
    handle.InputBegan:Connect(function(input)
        if input.UserInputType == Enum.UserInputType.MouseButton1 or input.UserInputType == Enum.UserInputType.Touch then
            dragging = true; dragStart = input.Position; startPos = gui.Position
            input.Changed:Connect(function() if input.UserInputState == Enum.UserInputState.End then dragging = false end end)
        end
    end)
    handle.InputChanged:Connect(function(input)
        if input.UserInputType == Enum.UserInputType.MouseMovement or input.UserInputType == Enum.UserInputType.Touch then dragInput = input end
    end)
    UserInputService.InputChanged:Connect(function(input)
        if input == dragInput and dragging then
            local delta = input.Position - dragStart
            gui.Position = UDim2.new(startPos.X.Scale, startPos.X.Offset + delta.X, startPos.Y.Scale, startPos.Y.Offset + delta.Y)
        end
    end)
end

-- Server Functions
local function TeleportToServer(mode)
    local Http = game:GetService("HttpService")
    local Api = "https://games.roblox.com/v1/games/" .. game.PlaceId .. "/servers/Public?sortOrder=" .. (mode == "Big" and "Desc" or "Asc") .. "&limit=100"
    local Success, Result = pcall(function() return Http:JSONDecode(game:HttpGet(Api)) end)
    if Success and Result.data then
        for _, server in pairs(Result.data) do
            if server.playing < server.maxPlayers and server.id ~= game.JobId then
                TeleportService:TeleportToPlaceInstance(game.PlaceId, server.id)
                break
            end
        end
    end
end

local function StartSonix()
    local inputFlags = {forward = false, back = false, left = false, right = false, up = false, down = false}
    
    local Main = Instance.new("Frame", sg)
    Main.Size = UDim2.new(0, 460, 0, 560); Main.Position = UDim2.new(0.5, -230, 0.5, -280)
    Main.BackgroundColor3 = Color3.fromRGB(250, 252, 255); Main.BorderSizePixel = 0
    Instance.new("UICorner", Main).CornerRadius = UDim.new(0, 15)
    local MainStroke = Instance.new("UIStroke", Main); MainStroke.Color = Color3.fromRGB(0, 120, 215); MainStroke.Thickness = 3
    
    -- Updated Header for SONER1X
    local Header = Instance.new("Frame", Main)
    Header.Size = UDim2.new(1, 0, 0, 90); Header.BackgroundColor3 = Color3.fromRGB(235, 242, 255); Header.BorderSizePixel = 0
    Instance.new("UICorner", Header).CornerRadius = UDim.new(0, 15)
    makeDraggable(Main, Header)

    local NickLabel = Instance.new("TextLabel", Header)
    NickLabel.Size = UDim2.new(1, 0, 0, 60); NickLabel.Position = UDim2.new(0, 0, 0, 0)
    NickLabel.Text = "SONER1X"; NickLabel.Font = Enum.Font.GothamBlack; NickLabel.TextSize = 34
    NickLabel.TextColor3 = Color3.fromRGB(0, 100, 200); NickLabel.BackgroundTransparency = 1

    -- Windows Icon
    local WinBtn = Instance.new("Frame", sg)
    WinBtn.Size = UDim2.new(0, 50, 0, 50); WinBtn.Position = UDim2.new(0.02, 0, 0.85, 0)
    WinBtn.BackgroundColor3 = Color3.fromRGB(0, 120, 215); Instance.new("UICorner", WinBtn)
    for i=0,3 do
        local s = Instance.new("Frame", WinBtn); s.Size = UDim2.new(0, 16, 0, 16)
        s.Position = UDim2.new((i%2==0 and 0.15 or 0.53), 0, (i<2 and 0.15 or 0.53), 0)
        s.BackgroundColor3 = Color3.new(1,1,1); s.BorderSizePixel = 0
    end
    makeDraggable(WinBtn, WinBtn)
    
    local dDist = 0; local isD = false
    WinBtn.InputBegan:Connect(function(i) if i.UserInputType == Enum.UserInputType.MouseButton1 or i.UserInputType == Enum.UserInputType.Touch then isD = true; dDist = 0 end end)
    WinBtn.InputEnded:Connect(function(i) if isD then isD = false; if dDist < 10 then Main.Visible = not Main.Visible end end end)
    UserInputService.InputChanged:Connect(function(i) if isD then dDist = dDist + 1 end end)

    -- Navigation
    local Nav = Instance.new("Frame", Header)
    Nav.Size = UDim2.new(1, 0, 0, 30); Nav.Position = UDim2.new(0, 0, 0, 55); Nav.BackgroundTransparency = 1

    local TabMain = Instance.new("TextButton", Nav); TabMain.Size = UDim2.new(0.5,0,1,0); TabMain.Text = "MAIN"; TabMain.Font = Enum.Font.GothamBold; TabMain.TextSize = 14; TabMain.BackgroundTransparency = 1; TabMain.TextColor3 = Color3.fromRGB(0, 120, 215)
    local TabMisc = Instance.new("TextButton", Nav); TabMisc.Size = UDim2.new(0.5,0,1,0); TabMisc.Position = UDim2.new(0.5,0,0,0); TabMisc.Text = "MISC"; TabMisc.Font = Enum.Font.GothamBold; TabMisc.TextSize = 14; TabMisc.BackgroundTransparency = 1; TabMisc.TextColor3 = Color3.fromRGB(150, 160, 180)
    
    local PageMain = Instance.new("ScrollingFrame", Main); PageMain.Size = UDim2.new(1, -20, 1, -110); PageMain.Position = UDim2.new(0, 10, 0, 100); PageMain.BackgroundTransparency = 1; PageMain.CanvasSize = UDim2.new(0, 0, 2, 0); PageMain.ScrollBarThickness = 0
    local PageMisc = Instance.new("ScrollingFrame", Main); PageMisc.Size = UDim2.new(1, -20, 1, -110); PageMisc.Position = UDim2.new(0, 10, 0, 100); PageMisc.BackgroundTransparency = 1; PageMisc.CanvasSize = UDim2.new(0, 0, 2, 0); PageMisc.Visible = false; PageMisc.ScrollBarThickness = 0

    TabMain.MouseButton1Click:Connect(function() PageMain.Visible = true; PageMisc.Visible = false; TabMain.TextColor3 = Color3.fromRGB(0, 120, 215); TabMisc.TextColor3 = Color3.fromRGB(150, 160, 180) end)
    TabMisc.MouseButton1Click:Connect(function() PageMain.Visible = false; PageMisc.Visible = true; TabMisc.TextColor3 = Color3.fromRGB(0, 120, 215); TabMain.TextColor3 = Color3.fromRGB(150, 160, 180) end)

    -- Adjust Menu
    local SetMenu = Instance.new("Frame", sg); SetMenu.Size = UDim2.new(0, 180, 0, 120); SetMenu.Position = UDim2.new(0.5, 240, 0.5, -60); SetMenu.BackgroundColor3 = Color3.new(1,1,1); SetMenu.Visible = false; Instance.new("UICorner", SetMenu); Instance.new("UIStroke", SetMenu).Color = Color3.fromRGB(0,120,215)
    local SetTitle = Instance.new("TextLabel", SetMenu); SetTitle.Size = UDim2.new(1,0,0,30); SetTitle.Text = "ADJUST"; SetTitle.BackgroundTransparency = 1
    local SetVal = Instance.new("TextLabel", SetMenu); SetVal.Size = UDim2.new(1,0,0,40); SetVal.Position = UDim2.new(0,0,0.3,0); SetVal.TextSize = 20; SetVal.TextColor3 = Color3.fromRGB(0,120,215); SetVal.BackgroundTransparency = 1
    local Minus = Instance.new("TextButton", SetMenu); Minus.Size = UDim2.new(0,40,0,40); Minus.Position = UDim2.new(0.1,0,0.6,0); Minus.Text = "-"; Minus.BackgroundColor3 = Color3.fromRGB(240,240,240); Instance.new("UICorner", Minus)
    local Plus = Instance.new("TextButton", SetMenu); Plus.Size = UDim2.new(0,40,0,40); Plus.Position = UDim2.new(0.7,0,0.6,0); Plus.Text = "+"; Plus.BackgroundColor3 = Color3.fromRGB(0,120,215); Plus.TextColor3 = Color3.new(1,1,1); Instance.new("UICorner", Plus)

    local function openSet(name, current, step, cb)
        if SetMenu.Visible and SetTitle.Text == name then SetMenu.Visible = false return end
        SetMenu.Visible = true; SetTitle.Text = name; SetVal.Text = tostring(current)
        local c1; local c2
        c1 = Minus.MouseButton1Click:Connect(function() current = current - step; SetVal.Text = tostring(current); cb(current) end)
        c2 = Plus.MouseButton1Click:Connect(function() current = current + step; SetVal.Text = tostring(current); cb(current) end)
        task.spawn(function() repeat task.wait() until not SetMenu.Visible or SetTitle.Text ~= name; c1:Disconnect(); c2:Disconnect() end)
    end

    local function addRow(parent, name, y, key, setCb)
        local b = Instance.new("TextButton", parent); b.Size = UDim2.new(0, 320, 0, 40); b.Position = UDim2.new(0, 5, 0, y); b.Text = name; b.Font = Enum.Font.Gotham; b.BorderSizePixel = 0; Instance.new("UICorner", b).CornerRadius = UDim.new(0, 8)
        local function refresh() b.BackgroundColor3 = Settings[key] and Color3.fromRGB(0, 120, 215) or Color3.fromRGB(225, 235, 245); b.TextColor3 = Settings[key] and Color3.new(1,1,1) or Color3.fromRGB(80, 100, 120) end
        refresh(); b.MouseButton1Click:Connect(function() Settings[key] = not Settings[key]; refresh() end)
        if setCb then
            local s = Instance.new("TextButton", parent); s.Size = UDim2.new(0, 60, 0, 40); s.Position = UDim2.new(0, 335, 0, y); s.Text = "SET"; s.BackgroundColor3 = Color3.fromRGB(200, 210, 225); Instance.new("UICorner", s); s.MouseButton1Click:Connect(setCb)
        end
    end

    local function addButton(parent, name, y, cb)
        local b = Instance.new("TextButton", parent); b.Size = UDim2.new(1, -10, 0, 40); b.Position = UDim2.new(0, 5, 0, y); b.Text = name; b.BackgroundColor3 = Color3.fromRGB(0, 120, 215); b.TextColor3 = Color3.new(1,1,1); b.Font = Enum.Font.GothamBold; Instance.new("UICorner", b)
        b.MouseButton1Click:Connect(cb)
    end

    -- Main Content
    addRow(PageMain, "Fly Hack", 0, "Fly", function() openSet("FLY SPEED", Settings.FlySpeed, 10, function(v) Settings.FlySpeed = v end) end)
    addRow(PageMain, "WalkSpeed", 50, "dummy", function() openSet("SPEED", Settings.WalkSpeed, 5, function(v) Settings.WalkSpeed = v end) end)
    addRow(PageMain, "Infinity Jump", 100, "InfJump")
    addRow(PageMain, "No Clip", 150, "NoClip")
    addRow(PageMain, "Aim Assist", 200, "AimLock")
    addRow(PageMain, "Hitbox Expander", 250, "Hitbox", function() openSet("HITBOX", Settings.HitboxSize, 2, function(v) Settings.HitboxSize = v end) end)

    -- Misc Content
    addRow(PageMisc, "Full Bright", 0, "FullBright")
    addRow(PageMisc, "Box ESP", 50, "BoxESP")
    addRow(PageMisc, "Chams (White)", 100, "Chams")
    addRow(PageMisc, "Spider Walk", 150, "Spider")
    addRow(PageMisc, "Auto Bhop", 200, "Bhop")
    
    addButton(PageMisc, "Big Server", 250, function() TeleportToServer("Big") end)
    addButton(PageMisc, "Small Server", 300, function() TeleportToServer("Small") end)
    addButton(PageMisc, "Rejoin Server", 350, function() TeleportService:TeleportToPlaceInstance(game.PlaceId, game.JobId) end)

    -- Fly UI
    local fUI = Instance.new("Frame", sg); fUI.Size = UDim2.new(1,0,1,0); fUI.BackgroundTransparency = 1; fUI.Visible = false
    local function fb(id, t, p)
        local b = Instance.new("TextButton", fUI); b.Size = UDim2.new(0,65,0,65); b.Position = p; b.Text = t; b.BackgroundColor3 = Color3.new(1,1,1); b.BackgroundTransparency = 0.2; b.TextColor3 = Color3.fromRGB(0,120,215); Instance.new("UICorner", b).CornerRadius = UDim.new(0,15); Instance.new("UIStroke", b).Color = Color3.fromRGB(0,120,215)
        b.MouseButton1Down:Connect(function() inputFlags[id] = true end)
        b.MouseButton1Up:Connect(function() inputFlags[id] = false end)
    end
    fb("forward", "UP", UDim2.new(0.08, 0, 0.65, 0)); fb("back", "DOWN", UDim2.new(0.08, 0, 0.8, 0))
    fb("left", "LEFT", UDim2.new(0.01, 0, 0.725, 0)); fb("right", "RIGHT", UDim2.new(0.15, 0, 0.725, 0))
    fb("up", "+", UDim2.new(0.85, 0, 0.65, 0)); fb("down", "-", UDim2.new(0.85, 0, 0.8, 0))

    -- Main Loop
    RunService.RenderStepped:Connect(function()
        local char = LocalPlayer.Character; local hrp = char and char:FindFirstChild("HumanoidRootPart"); local hum = char and char:FindFirstChildOfClass("Humanoid")
        if not (char and hrp and hum) then return end
        
        fUI.Visible = Settings.Fly
        if Settings.Fly then
            hum.WalkSpeed = 0; local m = Vector3.new(0,0,0)
            if inputFlags.forward then m = m + Camera.CFrame.LookVector end
            if inputFlags.back then m = m - Camera.CFrame.LookVector end
            if inputFlags.left then m = m - Camera.CFrame.RightVector end
            if inputFlags.right then m = m + Camera.CFrame.RightVector end
            if inputFlags.up then m = m + Vector3.new(0,1,0) end
            if inputFlags.down then m = m - Vector3.new(0,1,0) end
            hrp.Velocity = m * Settings.FlySpeed; hum.PlatformStand = true
        else
            hum.WalkSpeed = Settings.WalkSpeed; hum.PlatformStand = false
        end

        if Settings.Spider and hum.MoveDirection.Magnitude > 0 then
            local r = Ray.new(hrp.Position, hum.MoveDirection * 3)
            local part = workspace:FindPartOnRay(r, char)
            if part then hrp.Velocity = Vector3.new(hrp.Velocity.X, 35, hrp.Velocity.Z) end
        end
        if Settings.Bhop and hum.MoveDirection.Magnitude > 0 and hrp.Velocity.Y == 0 then hum:ChangeState(Enum.HumanoidStateType.Jumping) end

        if Settings.FullBright then
            Lighting.Brightness = 2; Lighting.ClockTime = 14; Lighting.Ambient = Color3.new(1,1,1)
        else
            Lighting.Brightness = OriginalLight.Brightness; Lighting.ClockTime = OriginalLight.ClockTime; Lighting.Ambient = OriginalLight.Ambient
        end

        for _, p in pairs(Players:GetPlayers()) do
            if p ~= LocalPlayer and p.Character then
                local tHRP = p.Character:FindFirstChild("HumanoidRootPart")
                if tHRP then
                    if Settings.Hitbox then
                        tHRP.Size = Vector3.new(Settings.HitboxSize, Settings.HitboxSize, Settings.HitboxSize)
                        tHRP.Transparency = 0.8; tHRP.Color = Color3.fromRGB(0, 162, 255); tHRP.CanCollide = false
                    else
                        tHRP.Size = Vector3.new(2, 2, 1); tHRP.Transparency = 1
                    end
                end
                local box = p.Character:FindFirstChild("SonixBox")
                if Settings.BoxESP then
                    if not box then
                        box = Instance.new("BoxHandleAdornment", p.Character); box.Name = "SonixBox"; box.Adornee = p.Character; box.AlwaysOnTop = true; box.ZIndex = 5; box.Size = Vector3.new(4, 6, 4); box.Color3 = Color3.fromRGB(0, 120, 215); box.Transparency = 0.6
                    end
                elseif box then box:Destroy() end
                local hl = p.Character:FindFirstChild("SonixHL")
                if Settings.Chams then
                    if not hl then
                        hl = Instance.new("Highlight", p.Character); hl.Name = "SonixHL"; hl.FillColor = Color3.new(1,1,1); hl.OutlineColor = Color3.fromRGB(0,120,215); hl.FillTransparency = 0.5
                    end
                elseif hl then hl:Destroy() end
            end
        end

        if Settings.AimLock then
            local target = nil; local mD = 400
            for _, p in pairs(Players:GetPlayers()) do
                if p ~= LocalPlayer and p.Character and p.Character:FindFirstChild("Head") then
                    local pos, vis = Camera:WorldToViewportPoint(p.Character.Head.Position)
                    if vis then
                        local mag = (Vector2.new(pos.X, pos.Y) - Vector2.new(Camera.ViewportSize.X/2, Camera.ViewportSize.Y/2)).Magnitude
                        if mag < mD then mD = mag; target = p.Character.Head end
                    end
                end
            end
            if target then Camera.CFrame = Camera.CFrame:Lerp(CFrame.new(Camera.CFrame.Position, target.Position), 0.1) end
        end

        for _, v in pairs(char:GetDescendants()) do if v:IsA("BasePart") then v.CanCollide = not Settings.NoClip end end
    end)
    
    UserInputService.JumpRequest:Connect(function()
        if Settings.InfJump and LocalPlayer.Character and LocalPlayer.Character:FindFirstChildOfClass("Humanoid") then
            LocalPlayer.Character:FindFirstChildOfClass("Humanoid"):ChangeState(Enum.HumanoidStateType.Jumping)
        end
    end)
end

-- Login
if isfile and isfile(SESSION_FILE) and (os.time() - HttpService:JSONDecode(readfile(SESSION_FILE)).Time < 3600) then
    StartSonix()
else
    local L = Instance.new("Frame", sg); L.Size = UDim2.new(0,300,0,200); L.Position = UDim2.new(0.5,-150,0.4,0); L.BackgroundColor3 = Color3.new(1,1,1); Instance.new("UICorner", L); makeDraggable(L, L)
    local LI = Instance.new("TextBox", L); LI.Size = UDim2.new(0,240,0,40); LI.Position = UDim2.new(0.5,-120,0.3,0); LI.PlaceholderText = "Enter Key"; LI.BackgroundColor3 = Color3.fromRGB(240,245,255); Instance.new("UICorner", LI)
    local LB = Instance.new("TextButton", L); LB.Size = UDim2.new(0,120,0,40); LB.Position = UDim2.new(0.5,-60,0.7,0); LB.Text = "LOGIN"; LB.BackgroundColor3 = Color3.fromRGB(0,120,215); LB.TextColor3 = Color3.new(1,1,1); Instance.new("UICorner", LB)
    LB.MouseButton1Click:Connect(function() if LI.Text == KEY_SYSTEM then if writefile then writefile(SESSION_FILE, HttpService:JSONEncode({Time = os.time()})) end; L:Destroy(); StartSonix() end end)
end
