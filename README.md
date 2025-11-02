-- Простой и быстрый скрипт
local Library = loadstring(game:HttpGet("https://raw.githubusercontent.com/xHeptc/Kavo-UI-Library/main/source.lua"))()
local Window = Library.CreateLib("Delta Menu", "Sentinel")

local Players = game:GetService("Players")
local RunService = game:GetService("RunService")
local UserInputService = game:GetService("UserInputService")
local LocalPlayer = Players.LocalPlayer
local Mouse = LocalPlayer:GetMouse()

-- Переменные
local TriggerBotEnabled = false
local AimAssistEnabled = false
local NoclipEnabled = false
local InfiniteJumpEnabled = false
local NoclipConnection = nil

-- Простой Trigger Bot
local function triggerBot()
    if not TriggerBotEnabled then return end
    
    local target = Mouse.Target
    if target and target.Parent then
        local humanoid = target.Parent:FindFirstChildOfClass("Humanoid")
        if humanoid then
            mouse1click()
        end
    end
end

-- Простой Aim Assist
local function aimAssist()
    if not AimAssistEnabled then return end
    
    for _, player in pairs(Players:GetPlayers()) do
        if player ~= LocalPlayer and player.Character and player.Character:FindFirstChild("HumanoidRootPart") then
            local distance = (LocalPlayer.Character.HumanoidRootPart.Position - player.Character.HumanoidRootPart.Position).Magnitude
            if distance < 20 then
                local targetPos = player.Character.HumanoidRootPart.Position
                workspace.CurrentCamera.CFrame = CFrame.lookAt(workspace.CurrentCamera.CFrame.Position, targetPos)
                break
            end
        end
    end
end

-- No Clip
local function toggleNoclip()
    NoclipEnabled = not NoclipEnabled
    if NoclipEnabled then
        NoclipConnection = RunService.Stepped:Connect(function()
            if LocalPlayer.Character then
                for _, part in pairs(LocalPlayer.Character:GetDescendants()) do
                    if part:IsA("BasePart") then
                        part.CanCollide = false
                    end
                end
            end
        end)
    else
        if NoclipConnection then
            NoclipConnection:Disconnect()
        end
    end
end

-- Infinite Jump
UserInputService.JumpRequest:Connect(function()
    if InfiniteJumpEnabled and LocalPlayer.Character then
        LocalPlayer.Character:FindFirstChildOfClass("Humanoid"):ChangeState("Jumping")
    end
end)

-- Обработчик клавиш
UserInputService.InputBegan:Connect(function(input)
    if input.KeyCode == Enum.KeyCode.R then
        TriggerBotEnabled = not TriggerBotEnabled
        print("Trigger Bot:", TriggerBotEnabled)
    end
    if input.KeyCode == Enum.KeyCode.Q then
        AimAssistEnabled = not AimAssistEnabled
        print("Aim Assist:", AimAssistEnabled)
    end
end)

-- Вкладки
local CombatTab = Window:NewTab("Combat")
local MovementTab = Window:NewTab("Movement")

-- Combat
local CombatSection = CombatTab:NewSection("Боевые функции")
CombatSection:NewToggle("Trigger Bot", "Авто-стрельба по игрокам", function(state)
    TriggerBotEnabled = state
end)

CombatSection:NewToggle("Aim Assist", "Авто-прицеливание", function(state)
    AimAssistEnabled = state
end)

CombatSection:NewLabel("Клавиши: R - Trigger Bot, Q - Aim Assist")

-- Movement
local MovementSection = MovementTab:NewSection("Передвижение")
MovementSection:NewToggle("No Clip", "Сквозь стены", function(state)
    toggleNoclip()
end)

MovementSection:NewToggle("Infinite Jump", "Бесконечные прыжки", function(state)
    InfiniteJumpEnabled = state
end)

-- Главный цикл
RunService.Heartbeat:Connect(function()
    triggerBot()
    aimAssist()
end)

print("Delta Menu загружен!")
