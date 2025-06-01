# Script-aimbot-BLOX-fruit
Aimbot blox
--[[
⚠️ Use por sua conta e risco
✅ Compatível com Hydrogen / Delta (Mobile)
🎯 Aimbot com mira + Auto Skills
📦 ESP com Box, Nome e Distância
📱 Com GUI Touch para ativar/desativar
]]

local Players = game:GetService("Players")
local LocalPlayer = Players.LocalPlayer
local RunService = game:GetService("RunService")
local Camera = workspace.CurrentCamera

-- CONFIGURAÇÕES
local aimPart = "HumanoidRootPart"
local skillKeys = {"Z", "X", "C", "V", "F"} -- Skills desejadas
local aiming = false
local autoSkill = false

-- ESP
function createESP(player)
    local box = Drawing.new("Square")
    box.Color = Color3.fromRGB(0, 255, 0)
    box.Thickness = 1
    box.Filled = false

    local nameTag = Drawing.new("Text")
    nameTag.Color = Color3.new(1, 1, 1)
    nameTag.Size = 16
    nameTag.Center = true
    nameTag.Outline = true

    RunService.RenderStepped:Connect(function()
        if player and player.Character and player.Character:FindFirstChild("HumanoidRootPart") then
            local pos, visible = Camera:WorldToViewportPoint(player.Character.HumanoidRootPart.Position)
            if visible then
                local dist = (Camera.CFrame.Position - player.Character.HumanoidRootPart.Position).Magnitude
                box.Size = Vector2.new(60, 100) / (dist / 100)
                box.Position = Vector2.new(pos.X - box.Size.X/2, pos.Y - box.Size.Y/2)
                box.Visible = true

                nameTag.Text = player.Name .. " [" .. math.floor(dist) .. "m]"
                nameTag.Position = Vector2.new(pos.X, pos.Y - 60)
                nameTag.Visible = true
            else
                box.Visible = false
                nameTag.Visible = false
            end
        else
            box.Visible = false
            nameTag.Visible = false
        end
    end)
end

-- Enemigo mais próximo
function getClosestEnemy()
    local closest, shortest = nil, math.huge
    for _, player in pairs(Players:GetPlayers()) do
        if player ~= LocalPlayer and player.Character and player.Character:FindFirstChild(aimPart) then
            local dist = (LocalPlayer.Character.HumanoidRootPart.Position - player.Character[aimPart].Position).Magnitude
            if dist < shortest then
                closest = player
                shortest = dist
            end
        end
    end
    return closest
end

-- Aimbot de Mira
RunService.RenderStepped:Connect(function()
    if aiming then
        local target = getClosestEnemy()
        if target and target.Character and target.Character:FindFirstChild(aimPart) then
            Camera.CFrame = CFrame.new(Camera.CFrame.Position, target.Character[aimPart].Position)
        end
    end
end)

-- Auto Skills
task.spawn(function()
    while true do
        task.wait(0.5)
        if autoSkill then
            local target = getClosestEnemy()
            if target and target.Character and target.Character:FindFirstChild(aimPart) then
                Camera.CFrame = CFrame.new(Camera.CFrame.Position, target.Character[aimPart].Position)
                for _, key in pairs(skillKeys) do
                    keypress(Enum.KeyCode[key])
                    task.wait(0.1)
                    keyrelease(Enum.KeyCode[key])
                end
            end
        end
    end
end)

-- ESP para todos os players
for _, p in pairs(Players:GetPlayers()) do
    if p ~= LocalPlayer then createESP(p) end
end

Players.PlayerAdded:Connect(function(p)
    if p ~= LocalPlayer then createESP(p) end
end)

-- GUI Touch para celular
local ScreenGui = Instance.new("ScreenGui", game.CoreGui)
ScreenGui.Name = "AimbotGui"
ScreenGui.ResetOnSpawn = false

function createButton(name, pos, callback)
    local button = Instance.new("TextButton")
    button.Size = UDim2.new(0, 120, 0, 40)
    button.Position = pos
    button.Text = name
    button.BackgroundColor3 = Color3.fromRGB(0, 170, 255)
    button.TextColor3 = Color3.new(1, 1, 1)
    button.TextScaled = true
    button.Parent = ScreenGui
    button.MouseButton1Click:Connect(callback)
end

createButton("🎯 Aimbot", UDim2.new(0, 20, 0, 100), function()
    aiming = not aiming
end)

createButton("🔥 Auto Skill", UDim2.new(0, 20, 0, 150), function()
    autoSkill = not autoSkill
end)
