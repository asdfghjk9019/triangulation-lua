local Polygon = {}

function Polygon.triangulate(vertices : {Vector3})
	local triangles = {}
	vertices = removeCollinear(vertices)

	if (#vertices < 3) then return triangles end
	if (#vertices > 1024) then return triangles end

	local r = getIndexOfleftmost(vertices)
	local q = r > 1 and r - 1 or #vertices
	local s = r < #vertices and r + 1 or 1
	
	local ccw = Polygon.ccw(vertices[q], vertices[r], vertices[s])
	
	if ccw then
		local tmp = {}
		for i = #vertices, 1, -1 do
			tmp[#tmp + 1] = vertices[i]
		end
		vertices = tmp
	end

	local indexList = {}
	for i = 1, #vertices do
		table.insert(indexList, i)
	end

	local totalTriangleCount = #vertices - 2

	while #indexList > 3 do
		for i = 1, #indexList do
			local a = indexList[i]
			local b = indexList[i == 1 and #indexList or i - 1]
			local c = indexList[i == #indexList and 1 or i + 1]

			local va = vertices[a]
			local vb = vertices[b]
			local vc = vertices[c]

			local vaToVb = vb - va
			local vaToVc = vc - va

			if vaToVb:Cross(vaToVc).Z < 0 then continue end
			local isEar = true

			for j = 1, #vertices do
				if (j == a) or (j == b) or (j == c) then continue end
				local p = vertices[j]

				if isPointInTriangle(p, vb, va, vc) then
					isEar = false
					break
				end
			end

			if isEar then
				table.insert(triangles, {vb, va, vc})
				table.remove(indexList, i)
				break
			end
		end
	end

	table.insert(triangles, {vertices[indexList[1]], vertices[indexList[2]], vertices[indexList[3]]})
	return triangles
end

function isPointInTriangle(p : Vector3, a : Vector3, b : Vector3, c : Vector3)
	local ab = b - a
	local bc = c - b
	local ca = a - c

	local ap = p - a
	local bp = p - b
	local cp = p - c

	local cross1 = ab:Cross(ap).Z
	local cross2 = bc:Cross(bp).Z
	local cross3 = ca:Cross(cp).Z

	if (cross1 > 0 or cross2 > 0 or cross3 > 0) then
		return false
	end

	return true
end

function areCollinear(p : Vector3, q : Vector3, r : Vector3, total : number)
	return (q - p):Cross(r - p).Z == 0
end

function Polygon.ccw(p : Vector3, q : Vector3, r : Vector3)
	return (q - p):Cross(r - p).Z >= 0
end

function Polygon.getCcw(vertices)
	local r = getIndexOfleftmost(vertices)
	local q = r > 1 and r - 1 or #vertices
	local s = r < #vertices and r + 1 or 1

	return Polygon.ccw(vertices[q], vertices[r], vertices[s])
end

function removeCollinear(vertices : { Vector3 })
	local result = {}
	local lines = {}

	for k = 1, #vertices do
		local i = k > 1 and k - 1 or #vertices
		local l = k < #vertices and k + 1 or 1

		local a = vertices[i]
		local b = vertices[k]
		local c = vertices[l]

		if not areCollinear(a, b, c, #vertices) then
			table.insert(result, vertices[k])
		end
	end

	return result
end

function getIndexOfleftmost(vertices : { Vector3 })
	local idx = 1

	for i = 2, #vertices do
		if vertices[i].X < vertices[idx].X then idx = i end
	end

	return idx
end

Polygon.removeCollinear = removeCollinear

return Polygon
