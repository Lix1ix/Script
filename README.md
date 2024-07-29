-- Create the GUI elements
local player = game.Players.LocalPlayer
local mouse = player:GetMouse()

local screenGui = Instance.new("ScreenGui")
screenGui.Parent = player:WaitForChild("PlayerGui")

-- Create the circle
local circle = Instance.new("ImageLabel")
circle.Size = UDim2.new(0, 50, 0, 50)
circle.Position = UDim2.new(0, 10, 0.5, -25)
circle.AnchorPoint = Vector2.new(0, 0.5)
circle.BackgroundColor3 = Color3.fromRGB(255, 0, 0)
circle.BackgroundTransparency = 1
circle.Image = "rbxassetid://6023426915" -- A circle image
circle.ImageColor3 = Color3.fromRGB(255, 0, 0)
circle.Parent = screenGui

-- Create the teleport message
local teleportMessage = Instance.new("TextLabel")
teleportMessage.Size = UDim2.new(0.5, 0, 0.1, 0)
teleportMessage.Position = UDim2.new(0.5, 0, 0, 0)
teleportMessage.AnchorPoint = Vector2.new(0.5, 0)
teleportMessage.BackgroundTransparency = 1
teleportMessage.Text = "Instant transmission"
teleportMessage.TextColor3 = Color3.fromRGB(255, 255, 255)
teleportMessage.TextScaled = true
teleportMessage.Font = Enum.Font.Arcade -- Change to any cool font you prefer
teleportMessage.Visible = false
teleportMessage.Parent = screenGui

local canTeleport = false

local function toggleTeleport()
    canTeleport = not canTeleport
    if canTeleport then
        circle.ImageColor3 = Color3.fromRGB(0, 255, 0) -- Green
    else
        circle.ImageColor3 = Color3.fromRGB(255, 0, 0) -- Red
    end
end

circle.InputBegan:Connect(function(input)
    if input.UserInputType == Enum.UserInputType.MouseButton1 or input.UserInputType == Enum.UserInputType.Touch then
        toggleTeleport()
    end
end)

local function teleportToPosition(position)
    if canTeleport then
        player.Character:SetPrimaryPartCFrame(CFrame.new(position))
        teleportMessage.Visible = true
        wait(0.75)
        teleportMessage.Visible = false
    end
end

mouse.Button1Down:Connect(function()
    local mousePosition = Vector2.new(mouse.X, mouse.Y)
    local circlePosition = circle.AbsolutePosition
    local circleSize = circle.AbsoluteSize

    if mousePosition.X >= circlePosition.X and mousePosition.X <= circlePosition.X + circleSize.X and
       mousePosition.Y >= circlePosition.Y and mousePosition.Y <= circlePosition.Y + circleSize.Y then
        return
    end

    teleportToPosition(mouse.Hit.p)
end)

game:GetService("UserInputService").TouchTap:Connect(function(touchPositions)
    local touchPosition = touchPositions[1]
    local circlePosition = circle.AbsolutePosition
    local circleSize = circle.AbsoluteSize

    if touchPosition.X >= circlePosition.X and touchPosition.X <= circlePosition.X + circleSize.X and
       touchPosition.Y >= circlePosition.Y and touchPosition.Y <= circlePosition.Y + circleSize.Y then
        return
    end

    local unitRay = workspace.CurrentCamera:ScreenPointToRay(touchPosition.X, touchPosition.Y)
    local ray = Ray.new(unitRay.Origin, unitRay.Direction * 1000)
    local hit, position = workspace:FindPartOnRay(ray)
    if hit then
        teleportToPosition(position)
    end
end)
