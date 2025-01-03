local Players = game:GetService("Players")
local RunService = game:GetService("RunService")
local UserInputService = game:GetService("UserInputService")
local StarterGui = game:GetService("StarterGui")

local hitboxSize = Vector3.new(100, 100, 100)
local hitboxTransparency = 0.5
local hitboxColor = BrickColor.new("Bright blue")
local neutralHitboxColor = BrickColor.new("Medium stone grey")
local hitboxEnabled = false
local enemyTeamColor = BrickColor.new("Bright red") -- Cor do time inimigo
local friendTeamColor = BrickColor.new("Bright blue") -- Cor do time amigo

local function createHitbox(character, teamColor)
    local hitbox = Instance.new("Part")
    hitbox.Size = hitboxSize
    hitbox.Transparency = hitboxTransparency
    hitbox.BrickColor = teamColor == nil and neutralHitboxColor or hitboxColor
    hitbox.Anchored = true
    hitbox.CanCollide = false
    hitbox.Parent = character

    local weld = Instance.new("WeldConstraint")
    weld.Part0 = hitbox
    weld.Part1 = character:FindFirstChild("HumanoidRootPart")
    weld.Parent = hitbox

    hitbox.Touched:Connect(function(hit)
        if hitboxEnabled then
            local humanoid = hit.Parent:FindFirstChildOfClass("Humanoid")
            local player = Players:GetPlayerFromCharacter(hit.Parent)
            if humanoid and player then
                if teamColor == nil or player.TeamColor == enemyTeamColor then
                    humanoid.Health = 0
                end
            end
        end
    end)

    return hitbox
end

local function toggleHitbox(character)
    hitboxEnabled = not hitboxEnabled
    local hitbox = character:FindFirstChild("Hitbox")
    if hitbox then
        hitbox.Transparency = hitboxEnabled and hitboxTransparency or 1
    end
end

local function boostFPS()
    settings().Rendering.QualityLevel = Enum.QualityLevel.Level01
end

local function createButton()
    local screenGui = Instance.new("ScreenGui")
    screenGui.Parent = StarterGui

    local button = Instance.new("TextButton")
    button.Size = UDim2.new(0, 200, 0, 50)
    button.Position = UDim2.new(0.5, -100, 0.9, -25)
    button.Text = "Toggle Hitbox"
    button.Parent = screenGui

    button.MouseButton1Click:Connect(function()
        local player = Players.LocalPlayer
        if player and player.Character then
            toggleHitbox(player.Character)
        end
    end)
end

Players.PlayerAdded:Connect(function(player)
    player.CharacterAdded:Connect(function(character)
        local teamColor = player.TeamColor
        if teamColor ~= friendTeamColor then
            local hitbox = createHitbox(character, teamColor)
            hitbox.Name = "Hitbox"
        end
    end)
end)

boostFPS()
createButton()
