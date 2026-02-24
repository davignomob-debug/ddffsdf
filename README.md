local Players = game:GetService("Players")
local UserInputService = game:GetService("UserInputService")
local LocalPlayer = Players.LocalPlayer

local sg = Instance.new("ScreenGui", (gethui and gethui()) or game:GetService("CoreGui"))
sg.Name = "NovoScript"

local Minimizado = false
local dragging = false
local dragInput = nil
local dragStart = nil
local startPos = nil
local dragTarget = nil

-- // FRAME PRINCIPAL
local Main = Instance.new("Frame", sg)
Main.Size = UDim2.fromOffset(320, 200)
Main.Position = UDim2.new(0.5, -160, 0.4, 0)
Main.BackgroundColor3 = Color3.fromRGB(15, 15, 15)
Main.BorderSizePixel = 0
Instance.new("UICorner", Main).CornerRadius = UDim.new(0, 10)

local stroke = Instance.new("UIStroke", Main)
stroke.Color = Color3.fromRGB(0, 255, 127)
stroke.Thickness = 2

-- // TOPBAR (area de arrastar)
local TopBar = Instance.new("Frame", Main)
TopBar.Size = UDim2.new(1, 0, 0, 45)
TopBar.BackgroundTransparency = 1

-- // TÍTULO
local Title = Instance.new("TextLabel", TopBar)
Title.Size = UDim2.new(1, -80, 1, 0)
Title.Text = "NOVO SCRIPT"
Title.TextColor3 = Color3.new(1, 1, 1)
Title.BackgroundTransparency = 1
Title.Font = Enum.Font.GothamBold
Title.TextSize = 18

-- // BOTÃO FECHAR
local CloseBtn = Instance.new("TextButton", TopBar)
CloseBtn.Size = UDim2.fromOffset(30, 30)
CloseBtn.Position = UDim2.new(1, -35, 0, 7)
CloseBtn.Text = "X"
CloseBtn.TextColor3 = Color3.new(1, 1, 1)
CloseBtn.BackgroundColor3 = Color3.fromRGB(40, 40, 40)
CloseBtn.Font = Enum.Font.GothamBold
CloseBtn.TextSize = 16
CloseBtn.BorderSizePixel = 0
Instance.new("UICorner", CloseBtn).CornerRadius = UDim.new(0, 6)

CloseBtn.MouseEnter:Connect(function()
    CloseBtn.BackgroundColor3 = Color3.fromRGB(180, 30, 30)
end)
CloseBtn.MouseLeave:Connect(function()
    CloseBtn.BackgroundColor3 = Color3.fromRGB(40, 40, 40)
end)
CloseBtn.MouseButton1Click:Connect(function()
    sg:Destroy()
end)

-- // BOTÃO MINIMIZAR
local MinBtn = Instance.new("TextButton", TopBar)
MinBtn.Size = UDim2.fromOffset(30, 30)
MinBtn.Position = UDim2.new(1, -70, 0, 7)
MinBtn.Text = "-"
MinBtn.TextColor3 = Color3.new(1, 1, 1)
MinBtn.BackgroundColor3 = Color3.fromRGB(40, 40, 40)
MinBtn.Font = Enum.Font.GothamBold
MinBtn.TextSize = 20
MinBtn.BorderSizePixel = 0
Instance.new("UICorner", MinBtn).CornerRadius = UDim.new(0, 6)

MinBtn.MouseEnter:Connect(function()
    MinBtn.BackgroundColor3 = Color3.fromRGB(180, 150, 0)
end)
MinBtn.MouseLeave:Connect(function()
    MinBtn.BackgroundColor3 = Color3.fromRGB(40, 40, 40)
end)

-- // FRAME MINIMIZADO
local Mini = Instance.new("TextButton", sg)
Mini.Size = UDim2.fromOffset(50, 50)
Mini.Position = Main.Position
Mini.BackgroundColor3 = Color3.fromRGB(15, 15, 15)
Mini.BorderSizePixel = 0
Mini.Text = "▣"
Mini.TextColor3 = Color3.fromRGB(0, 255, 127)
Mini.Font = Enum.Font.GothamBold
Mini.TextSize = 22
Mini.Visible = false
Instance.new("UICorner", Mini).CornerRadius = UDim.new(0, 12)

local miniStroke = Instance.new("UIStroke", Mini)
miniStroke.Color = Color3.fromRGB(0, 255, 127)
miniStroke.Thickness = 2

-- // CONTEÚDO
local Content = Instance.new("Frame", Main)
Content.Size = UDim2.new(1, 0, 1, -50)
Content.Position = UDim2.new(0, 0, 0, 50)
Content.BackgroundTransparency = 1

-- // BOTÃO QUE NÃO FAZ NADA
local Btn = Instance.new("TextButton", Content)
Btn.Size = UDim2.new(0.9, 0, 0, 48)
Btn.Position = UDim2.new(0.05, 0, 0.1, 0)
Btn.Text = "BOTÃO"
Btn.BackgroundColor3 = Color3.fromRGB(35, 35, 35)
Btn.TextColor3 = Color3.new(1, 1, 1)
Btn.Font = Enum.Font.GothamBold
Btn.TextSize = 17
Btn.BorderSizePixel = 0
Instance.new("UICorner", Btn)

Btn.MouseEnter:Connect(function()
    Btn.BackgroundColor3 = Color3.fromRGB(50, 50, 50)
end)
Btn.MouseLeave:Connect(function()
    Btn.BackgroundColor3 = Color3.fromRGB(35, 35, 35)
end)

-- // MINIMIZAR
MinBtn.MouseButton1Click:Connect(function()
    Minimizado = true
    Mini.Position = Main.Position
    Main.Visible = false
    Mini.Visible = true
end)

-- // RESTAURAR
Mini.MouseButton1Click:Connect(function()
    Minimizado = false
    Main.Position = Mini.Position
    Main.Visible = true
    Mini.Visible = false
end)

-- // ARRASTAR - TOPBAR
TopBar.InputBegan:Connect(function(input)
    if input.UserInputType == Enum.UserInputType.MouseButton1 then
        dragging = true
        dragTarget = Main
        dragStart = input.Position
        startPos = Main.Position
    end
end)
TopBar.InputChanged:Connect(function(input)
    if input.UserInputType == Enum.UserInputType.MouseMovement then
        dragInput = input
    end
end)

-- // ARRASTAR - MINI
Mini.InputBegan:Connect(function(input)
    if input.UserInputType == Enum.UserInputType.MouseButton1 then
        dragging = true
        dragTarget = Mini
        dragStart = input.Position
        startPos = Mini.Position
    end
end)
Mini.InputChanged:Connect(function(input)
    if input.UserInputType == Enum.UserInputType.MouseMovement then
        dragInput = input
    end
end)

-- // MOVER
UserInputService.InputChanged:Connect(function(input)
    if input == dragInput and dragging and dragTarget then
        local delta = input.Position - dragStart
        dragTarget.Position = UDim2.new(
            startPos.X.Scale,
            startPos.X.Offset + delta.X,
            startPos.Y.Scale,
            startPos.Y.Offset + delta.Y
        )
    end
end)

UserInputService.InputEnded:Connect(function(input)
    if input.UserInputType == Enum.UserInputType.MouseButton1 then
        dragging = false
        dragTarget = nil
    end
end)
