local Players = game:GetService("Players")
local TweenService = game:GetService("TweenService")
local UserInputService = game:GetService("UserInputService")

local ScreenGui = Instance.new("ScreenGui")
ScreenGui.Name = "ItemSpawnerGui"
ScreenGui.Parent = game.CoreGui

local MainFrame = Instance.new("Frame")
MainFrame.Name = "MainFrame"
MainFrame.Size = UDim2.new(0, 250, 0, 300)
MainFrame.Position = UDim2.new(0.8, 0, 0.5, -150)
MainFrame.BackgroundColor3 = Color3.fromRGB(30, 30, 30)
MainFrame.BorderSizePixel = 0
MainFrame.Parent = ScreenGui

local UICorner = Instance.new("UICorner")
UICorner.CornerRadius = UDim.new(0, 8)
UICorner.Parent = MainFrame

local Title = Instance.new("TextLabel")
Title.Size = UDim2.new(1, 0, 0, 30)
Title.BackgroundTransparency = 1
Title.Text = "Item Spawner"
Title.TextColor3 = Color3.fromRGB(255, 255, 255)
Title.TextSize = 16
Title.Font = Enum.Font.GothamBold
Title.Parent = MainFrame

local SearchBox = Instance.new("TextBox")
SearchBox.Size = UDim2.new(0.9, 0, 0, 30)
SearchBox.Position = UDim2.new(0.05, 0, 0, 40)
SearchBox.BackgroundColor3 = Color3.fromRGB(45, 45, 45)
SearchBox.TextColor3 = Color3.fromRGB(255, 255, 255)
SearchBox.PlaceholderText = "ค้นหาไอเทม..."
SearchBox.TextSize = 14
SearchBox.Font = Enum.Font.Gotham
SearchBox.Parent = MainFrame

local ItemList = Instance.new("ScrollingFrame")
ItemList.Size = UDim2.new(0.9, 0, 0, 180)
ItemList.Position = UDim2.new(0.05, 0, 0, 80)
ItemList.BackgroundColor3 = Color3.fromRGB(35, 35, 35)
ItemList.BorderSizePixel = 0
ItemList.ScrollBarThickness = 4
ItemList.Parent = MainFrame

local AmountBox = Instance.new("TextBox")
AmountBox.Size = UDim2.new(0.9, 0, 0, 30)
AmountBox.Position = UDim2.new(0.05, 0, 0, 270)
AmountBox.BackgroundColor3 = Color3.fromRGB(45, 45, 45)
AmountBox.TextColor3 = Color3.fromRGB(255, 255, 255)
AmountBox.PlaceholderText = "จำนวน (ค่าเริ่มต้น: 1)"
AmountBox.Text = "1"
AmountBox.TextSize = 14
AmountBox.Font = Enum.Font.Gotham
AmountBox.Parent = MainFrame

for _, element in pairs({SearchBox, ItemList, AmountBox}) do
    local corner = Instance.new("UICorner")
    corner.CornerRadius = UDim.new(0, 6)
    corner.Parent = element
end

-- รายการไอเทมพร้อมรูปร่าง
local itemsList = {
    {name = "Sword", meshId = "rbxassetid://121944778"},
    {name = "Shield", meshId = "rbxassetid://91068475"},
    {name = "Potion", meshId = "rbxassetid://11202779"},
    {name = "Key", meshId = "rbxassetid://13172546"},
    {name = "Crown", meshId = "rbxassetid://1365766"},
    {name = "Staff", meshId = "rbxassetid://11999247"},
    {name = "Orb", meshId = "rbxassetid://1158007"},
    {name = "Book", meshId = "rbxassetid://1943758"},
    {name = "Ring", meshId = "rbxassetid://3270017"},
    {name = "Chest", meshId = "rbxassetid://1290033"}
}

local function createItemButton(itemData, position)
    local Button = Instance.new("TextButton")
    Button.Size = UDim2.new(0.9, 0, 0, 30)
    Button.Position = position
    Button.BackgroundColor3 = Color3.fromRGB(45, 45, 45)
    Button.TextColor3 = Color3.fromRGB(255, 255, 255)
    Button.Text = itemData.name
    Button.TextSize = 14
    Button.Font = Enum.Font.Gotham
    Button.Parent = ItemList
    
    local ButtonCorner = Instance.new("UICorner")
    ButtonCorner.CornerRadius = UDim.new(0, 6)
    ButtonCorner.Parent = Button
    
    Button.MouseButton1Click:Connect(function()
        local amount = tonumber(AmountBox.Text) or 1
        for i = 1, amount do
            local item = Instance.new("Part")
            item.Name = itemData.name
            item.Size = Vector3.new(2, 2, 2)
            item.BrickColor = BrickColor.Random()
            item.Position = game.Players.LocalPlayer.Character.HumanoidRootPart.Position + Vector3.new(0, 5, 0)
            
            -- เพิ่ม Mesh
            local mesh = Instance.new("SpecialMesh")
            mesh.MeshType = Enum.MeshType.FileMesh
            mesh.MeshId = itemData.meshId
            mesh.Parent = item
            
            -- แสดงชื่อไอเทม
            local itemName = Instance.new("BillboardGui")
            itemName.Size = UDim2.new(0, 100, 0, 20)
            itemName.StudsOffset = Vector3.new(0, 2, 0)
            itemName.Parent = item

            local nameLabel = Instance.new("TextLabel")
            nameLabel.Size = UDim2.new(1, 0, 1, 0)
            nameLabel.BackgroundTransparency = 1
            nameLabel.Text = itemData.name
            nameLabel.TextColor3 = Color3.new(1, 1, 1)
            nameLabel.TextStrokeTransparency = 0
            nameLabel.Parent = itemName
            
            item.Parent = workspace
        end
    end)
    
    Button.MouseEnter:Connect(function()
        TweenService:Create(Button, TweenInfo.new(0.2), {
            BackgroundColor3 = Color3.fromRGB(60, 60, 60)
        }):Play()
    end)
    
    Button.MouseLeave:Connect(function()
        TweenService:Create(Button, TweenInfo.new(0.2), {
            BackgroundColor3 = Color3.fromRGB(45, 45, 45)
        }):Play()
    end)
    
    return Button
end

local function displayItems(filter)
    for _, child in ipairs(ItemList:GetChildren()) do
        if child:IsA("TextButton") then
            child:Destroy()
        end
    end
    
    local yPos = 5
    for _, itemData in ipairs(itemsList) do
        if not filter or string.find(string.lower(itemData.name), string.lower(filter)) then
            createItemButton(itemData, UDim2.new(0.05, 0, 0, yPos))
            yPos = yPos + 35
        end
    end
    
    ItemList.CanvasSize = UDim2.new(0, 0, 0, yPos)
end

SearchBox.Changed:Connect(function(prop)
    if prop == "Text" then
        displayItems(SearchBox.Text)
    end
end)

local isDragging = false
local dragStart = nil
local startPos = nil

MainFrame.InputBegan:Connect(function(input)
    if input.UserInputType == Enum.UserInputType.MouseButton1 then
        isDragging = true
        dragStart = input.Position
        startPos = MainFrame.Position
    end
end)

UserInputService.InputChanged:Connect(function(input)
    if input.UserInputType == Enum.UserInputType.MouseMovement and isDragging then
        local delta = input.Position - dragStart
        MainFrame.Position = UDim2.new(
            startPos.X.Scale,
            startPos.X.Offset + delta.X,
            startPos.Y.Scale,
            startPos.Y.Offset + delta.Y
        )
    end
end)

UserInputService.InputEnded:Connect(function(input)
    if input.UserInputType == Enum.UserInputType.MouseButton1 then
        isDragging = false
    end
end)

displayItems()
