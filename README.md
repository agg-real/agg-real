Print("Made by gugugaga_sukuna")
local OrionLib = loadstring(game:HttpGet(('https://raw.githubusercontent.com/ionlyusegithubformcmods/1-Line-Scripts/main/Mobile%20Friendly%20Orion')))()
local UserInputService = game:GetService("UserInputService")
local TweenService = game:GetService("TweenService")
local RunService = game:GetService("RunService")
local LocalPlayer = game:GetService("Players").LocalPlayer
local Mouse = LocalPlayer:GetMouse()
local HttpService = game:GetService("HttpService")

local Window = OrionLib:MakeWindow({Name = "Перчатка", HidePremium = false, SaveConfig = true})
local Tab = Window:MakeTab({Name = "Настройки", Icon = "Settings"})
local power = 50
local speed = 0
local flightEnabled = false
local dragEnabled = true

local powerTextbox = Tab:AddTextbox({
    Name = "Мощность",
    Default = tostring(power),
    Callback = function(value)
        local num = tonumber(value)
        if num then
            power = num
        else
            powerTextbox:Set(tostring(power))
        end
    end
})

local speedTextbox = Tab:AddTextbox({
    Name = "Скорость",
    Default = tostring(speed),
    Callback = function(value)
        local num = tonumber(value)
        if num then
            speed = num
        else
            speedTextbox:Set(tostring(speed))
        end
    end
})

local flightToggle = Tab:AddToggle({
    Name = "Включить massless изменить размер и FlightSpeed кстати салойбратикврайта",
    Default = flightEnabled,
    Callback = function(value)
        flightEnabled = value
    end
})

local dragToggle = Tab:AddToggle({
    Name = "Перетаскивание меню",
    Default = dragEnabled,
    Callback = function(value)
        dragEnabled = value
    end
})

Tab:AddButton({
    Name = "Сброс к дефолту",
    Callback = function()
        power = 5
        speed = 0.7
        powerTextbox:Set(tostring(power / 10))
        speedTextbox:Set(tostring(speed))
    end
})

task.spawn(function()
    while task.wait() do
        if game.Players.LocalPlayer.Character and game.Players.LocalPlayer.Character:FindFirstChild("Brick") then
            local brick = game.Players.LocalPlayer.Character.Brick
            brick.Speed.Value = speed
            brick.Power.Value = power / 10
            if flightEnabled then
                brick.Handle.Massless = true
                brick.Handle.Size = Vector3.new(1254, math.random(1, 10), math.random(1, 10))
                brick.FlightSpeed.Value = 100000000000000
            else
                brick.Handle.Massless = false
                brick.Handle.Size = Vector3.new(1.7042549848556519, 2.625422477722168, 0.33697208762168884)
                brick.FlightSpeed.Value = 0.52
            end
        end
    end
end)

OrionLib:Init()

local function MakeDraggable(DragPoint, Main)
    local Dragging, DragInput, MousePos, FramePos = false
    DragPoint.InputBegan:Connect(function(Input)
        if Input.UserInputType == Enum.UserInputType.MouseButton1 or Input.UserInputType == Enum.UserInputType.Touch then
            Dragging = true
            MousePos = Input.Position
            FramePos = Main.Position
            Input.Changed:Connect(function()
                if Input.UserInputState == Enum.UserInputState.End then
                    Dragging = false
                end
            end)
        end
    end)
    DragPoint.InputChanged:Connect(function(Input)
        if Input.UserInputType == Enum.UserInputType.MouseMovement or Input.UserInputType == Enum.UserInputType.Touch then
            DragInput = Input
        end
    end)
    UserInputService.InputChanged:Connect(function(Input)
        if Input == DragInput and Dragging and dragEnabled then
            local Delta = Input.Position - MousePos
            TweenService:Create(Main, TweenInfo.new(0.05, Enum.EasingStyle.Quint, Enum.EasingDirection.Out), {Position = UDim2.new(FramePos.X.Scale, FramePos.X.Offset + Delta.X, FramePos.Y.Scale, FramePos.Y.Offset + Delta.Y)}):Play()
            Main.Position = UDim2.new(FramePos.X.Scale, FramePos.X.Offset + Delta.X, FramePos.Y.Scale, FramePos.Y.Offset + Delta.Y)
        end
    end)
end

MakeDraggable(Window.Main.TopBar, Window.Main)
