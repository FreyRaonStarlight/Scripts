local Workspace = game:GetService("Workspace")

-- Models to exclude
local excludedNames = {
	FreyRaonStarlight = true,
	TarouRui = true
}

-- Function to create a custom highlight (a box around the part)
local function createCustomHighlight(part)
	local box = Instance.new("BoxHandleAdornment")
	box.Adornee = part
	box.Size = part.Size
	box.Color3 = Color3.fromRGB(255, 255, 0) -- Yellow
	box.Transparency = 0.5
	box.AlwaysOnTop = true
	box.ZIndex = 10
	box.Name = "CustomHighlight"
	box.Parent = part
end

-- Determine if a model qualifies for highlighting
local function isTargetModel(model)
	if not model:IsA("Model") then return false end
	if excludedNames[model.Name] then return false end
	local head = model:FindFirstChild("Head")
	return head and head:IsA("BasePart")
end

-- Highlight each BasePart in the model
local function applyHighlightToModel(model)
	if not isTargetModel(model) then return end

	for _, part in ipairs(model:GetDescendants()) do
		if part:IsA("BasePart") and not part:FindFirstChild("CustomHighlight") then
			createCustomHighlight(part)
		end
	end
end

-- Initial scan
for _, model in ipairs(Workspace:GetChildren()) do
	applyHighlightToModel(model)
end

-- Watch for new models being added
Workspace.ChildAdded:Connect(function(child)
	if not child:IsA("Model") or excludedNames[child.Name] then return end

	-- Check if it already has a Head part
	if child:FindFirstChild("Head") then
		applyHighlightToModel(child)
	end

	-- Watch for Head being added later
	child.ChildAdded:Connect(function(part)
		if part.Name == "Head" and part:IsA("BasePart") then
			applyHighlightToModel(child)
		end
	end)
end)
