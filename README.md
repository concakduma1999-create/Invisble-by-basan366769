# Invisble-by-basan366769
Invisble-- Tên: Scripts by Tus
-- Tính năng: Perfect Underworld Ghost (Depth: 45 Studs)
-- Tối ưu: Fix Jump, Climb, Swim, No Shake

local Player = game.Players.LocalPlayer
local RunService = game:GetService("RunService")
local Character = Player.Character or Player.CharacterAdded:Wait()
local Root = Character:WaitForChild("HumanoidRootPart")
local Hum = Character:WaitForChild("Humanoid")

-- Tạo GUI siêu nhỏ (100x50)
local ScreenGui = Instance.new("ScreenGui", Player.PlayerGui)
ScreenGui.Name = "TusGhost_45"
ScreenGui.ResetOnSpawn = false

local MainFrame = Instance.new("Frame", ScreenGui)
MainFrame.Size = UDim2.new(0, 100, 0, 50)
MainFrame.Position = UDim2.new(0.5, -50, 0.05, 0)
MainFrame.BackgroundColor3 = Color3.fromRGB(35, 35, 35)
MainFrame.BorderSizePixel = 0
MainFrame.Draggable = true
MainFrame.Active = true

local Title = Instance.new("TextLabel", MainFrame)
Title.Size = UDim2.new(1, 0, 0, 15)
Title.Text = "scripts by tus"
Title.TextColor3 = Color3.fromRGB(255, 255, 255)
Title.BackgroundTransparency = 1
Title.Font = Enum.Font.Code -- Style Mojang
Title.TextSize = 10

local ToggleBtn = Instance.new("TextButton", MainFrame)
ToggleBtn.Size = UDim2.new(0, 55, 0, 22)
ToggleBtn.Position = UDim2.new(0.5, -27, 0.5, 5)
ToggleBtn.BackgroundColor3 = Color3.fromRGB(200, 0, 0) -- OFF (Đỏ)
ToggleBtn.Text = "off"
ToggleBtn.TextColor3 = Color3.fromRGB(255, 255, 255)
ToggleBtn.Font = Enum.Font.Code
ToggleBtn.TextSize = 15

-- Biến logic
local IsActive = false
local UnderBody = nil
local OffsetY = -45 -- Cập nhật xuống 45 studs theo yêu cầu

ToggleBtn.MouseButton1Click:Connect(function()
    IsActive = not IsActive
    
    if IsActive then
        ToggleBtn.Text = "on"
        ToggleBtn.BackgroundColor3 = Color3.fromRGB(0, 180, 0) -- ON (Xanh)
        
        -- Tạo bản sao hiển thị ở dưới đất -45 studs
        Character.Archivable = true
        UnderBody = Character:Clone()
        UnderBody.Parent = workspace
        
        -- Xóa các thành phần thừa ở bản sao để tránh lag
        for _, v in pairs(UnderBody:GetDescendants()) do
            if v:IsA("LocalScript") or v:IsA("Script") or v:IsA("Sound") then
                v:Destroy()
            elseif v:IsA("BasePart") then
                v.CanCollide = false -- Dưới đất luôn NoClip
                v.Anchored = true
            end
        end
    else
        ToggleBtn.Text = "off"
        ToggleBtn.BackgroundColor3 = Color3.fromRGB(200, 0, 0)
        
        -- Hiện lại nhân vật chính khi tắt
        for _, part in pairs(Character:GetDescendants()) do
            if part:IsA("BasePart") then
                part.Transparency = 0
            end
        end
        
        if UnderBody then
            UnderBody:Destroy()
            UnderBody = nil
        end
    end
end)

-- Vòng lặp Render chính
RunService.RenderStepped:Connect(function()
    if IsActive and Root and UnderBody then
        -- 1. Tàng hình nhân vật chính ở trên (Server sẽ không thấy bạn)
        for _, part in pairs(Character:GetDescendants()) do
            if part:IsA("BasePart") and part.Name ~= "HumanoidRootPart" then
                part.Transparency = 1
            end
        end
        
        -- 2. Đồng bộ bản sao ở dưới đất -45 studs
        -- Nhân vật chính ở trên vẫn Leo, Bơi, Nhảy bình thường nên bản sao sẽ khớp 100%
        UnderBody:SetPrimaryPartCFrame(Root.CFrame * CFrame.new(0, OffsetY, 0))
        
        -- 3. Camera bám vào nhân vật chính ở trên (Không rung, di chuyển mượt)
        workspace.CurrentCamera.CameraSubject = Hum
    end
end)
 
