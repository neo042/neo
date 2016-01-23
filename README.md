if GetObjectName(GetMyHero()) ~= "Tryndamere" then return end
--REQUIRED LIBRARIES
if not pcall( require, "Inspired" ) then PrintChat("You are missing Inspired.lua - Go download it and save it Common!") return end
if not pcall( require, "IsFacing" ) then PrintChat("You are missing IsFacing.lua - Go download it and save it in Common!") return end
if not pcall( require, "DamageLib" ) then PrintChat("You are missing DamageLib.lua - Go download it and save it in Common!") return end
if not pcall( require, "Deftlib" ) then PrintChat("You are missing Deftlib.lua - Go download it and save it in Common!") return end
--GoS MENU
local HonTrynda = MenuConfig("Honest Tryndaere v0.2", "Tryndamere")
HonTrynda:Menu("Combo", "Combo")
		HonTrynda.Combo:Boolean("Q", "Use Auto Q", true)
		HonTrynda.Combo:Slider("HP","Q when Health is low than...", 60, 1, 100, 1)
		HonTrynda.Combo:Boolean("W", "Use W", true)
		HonTrynda.Combo:Boolean("E", "Use E", true)
		HonTrynda.Combo:Boolean("R", "Use Auto R", true)
		HonTrynda.Combo:Slider("RP", "R when Health is low than...", 35, 1, 100, 1)

HonTrynda:Menu("Killsteal", "Killsteal")
		HonTrynda.Killsteal:Boolean("E", "KillSteal with E?", true)

HonTrynda:Menu("Misc", "Misc")
--HonTrynda.Misc:Boolean("AutoIgnite", "Use Auto-Ignite?", true)
		HonTrynda.Misc:Boolean("AutoIgnite", "Use Auto-Ignite?", true)
HonTrynda:Menu("DMG", "Draw Damage")
		HonTrynda.DMG:Boolean("E", "Draw E Damage?", true)		

--HonTrynda.Misc:Boolean("AutoItem", "Use Auto-Item?", true)----in S6 will be available

HonTrynda.Misc:Boolean("Autolvl", "Use Auto-Level Up?", true)
		HonTrynda.Misc:List("Autolvl", "Priority", 1, {"E-Q-Q", "E-Q-W"})
		
HonTrynda:SubMenu("Drawings", "Draw Areas")
		HonTrynda.Drawings:Boolean("W", "Draw area of W?", true)
		HonTrynda.Drawings:Boolean("E", "Draw area of E?", true)
		HonTrynda.Drawings:ColorPick("color", "Color Picker", {255,255,255,0})

--RANGE OF W & E
OnDraw(function(myHero)
	local col = HonTrynda.Drawings.color:Value()
	if HonTrynda.Drawings.W:Value() then DrawCircle(myHeroPos(),855,3,100,col) end
	if HonTrynda.Drawings.E:Value() then DrawCircle(myHeroPos(),660,3,100,col) end
end)
	
OnTick(function(myHero)
	local target = GetCurrentTarget()
--COMBO CONFIG
	if IOW:Mode() == "Combo" then

		local EPred = GetPredictionForPlayer(myHeroPos(),target,GetMoveSpeed(target),1300,250,1200,120,false,false)

		if CanUseSpell(myHero,_Q) == READY and HonTrynda.Combo.Q:Value() and (GetCurrentHP(myHero)/GetMaxHP(myHero))*100 <= HonTrynda.Combo.HP:Value() and ValidTarget(target, 1000) and GotBuff(myHero,"UndyingRage") == 0
			then CastSpell(_Q)                                             
			end
			
		if CanUseSpell(myHero,_W) == READY and HonTrynda.Combo.W:Value() and not IsFacing(target) and ValidTarget(target, 400)
			then CastSpell(_W)
			end		
						
		if CanUseSpell(myHero,_E) == READY and EPred.HitChance == 1 and ValidTarget(target,GetCastRange(myHero,_E)) and HonTrynda.Combo.E:Value() then
			CastSkillShot(_E,EPred.PredPos.x,EPred.PredPos.y,EPred.PredPos.z)
			end
			
		if CanUseSpell(myHero,_R) == READY and (GetCurrentHP(myHero)/GetMaxHP(myHero)) <= 0.35 and HonTrynda.Combo.R:Value() then
			CastSpell(_R)
			end	
	
		if HonTrynda.Combo.R:Value() and EnemiesAround(myHeroPos(), 800) > 0 and HonTrynda.Combo.RP:Value() then
			CastSpell(_R)
			end
			
	end
--Deftsu's IGNITE CODE & KS
	for i,enemy in pairs(GetEnemyHeroes()) do
	
--if Ignite and HonTrynda.Misc.AutoIgnite:Value() then
--if CanUseSpell(myHero, Ignite) == READY and 20*GetLevel(myHero)+50 > GetCurrentHP(enemy)+GetHPRegen(enemy)*2.5 and GetDistanceSqr(GetOrigin(enemy)) < 600*600 then
--CastTargetSpell(enemy, Ignite)
-- end
--end
	if Ignite and HonTrynda.Misc.AutoIgnite:Value() then
        if IsReady(Ignite) and 20*GetLevel(myHero)+50 > GetHP(enemy)+GetHPRegen(enemy)*2.5 and ValidTarget(enemy, 600) then
         CastTargetSpell(enemy, Ignite)
         end
    end

--deftsu's KS "E" code
	if IsReady(_E) and ValidTarget(enemy, 660) and HonTrynda.Killsteal.E:Value() and GetHP2(enemy) < getdmg("E",enemy) then  
			Cast(_E,enemy)    
    end
		
	end

--Deftsu's Autoitem--- in S6 will be available
--if HonTrynda.Misc.AutoItem:Value() then

--if IOW:Mode() == "Combo" then
--if GetItemSlot(myHero, 3153) > 0 and IsReady (GetItemSlot(myHero, 3153)) and HonTrynda.Combo.E:Value() and IsReady(_E) and ValidTarget(target, 660) then CastTargetSpell (target, GetItemSlot(myHero,3153)) end
--if GetItemSlot(myHero, 3153) > 0 and IsReady (GetItemSlot(myHero, 3153)) and GetPercentHP(myHero) < 30 and ValidTarget(target, 660) then CastTargetSpell (target, GetItemSlot(myHero,3153)) end
--if GetItemSlot(myHero, 3144) > 0 and IsReady (GetItemSlot(myHero, 3144)) and HonTrynda.Combo.E:Value() and IsReady(_E) and ValidTarget(target, 660) then CastTargetSpell (target, GetItemSlot(myHero,3144)) end
--end
		
--end

--Deftsu's damage
	for i,enemy in pairs(GetEnemyHeroes()) do

		if ValidTarget(enemy, 660) and HonTrynda.DMG.E:Value() then
		local trueDMG = CalcDamage(myHero, enemy, (30*GetCastLevel(myHero,_E) - 40+1.2*(GetBaseDamage(myHero) + GetBonusDmg(myHero))),0)
			DrawDmgOverHpBar(enemy,GetCurrentHP(enemy),trueDMG,0,0xffffff00)
		end
		
	end

--LEVELTABLE
	if HonTrynda.Misc.Autolvl:Value() then

		if HonTrynda.Misc.Autolvl:Value() == 1 then leveltable = {_E, _Q, _Q, _W, _Q, _R, _Q, _E, _Q, _E, _R, _E, _E, _W, _W, _R, _W, _W}
		elseif HonTrynda.Misc.Autolvl:Value() == 2 then leveltable = {_E, _Q, _W, _Q, _Q, _R, _Q, _W, _Q, _W, _R, _W, _W, _E, _E, _R, _E, _E}
		end
		LevelSpell(leveltable[GetLevel(myHero)])
	
	end

end)

--CHAT TEXT (info)
PrintChat("<font color='#FF0000'>Honest HonTryndaere v0.2</font><font color='#FFFF00'> by neo https://raw.githubusercontent.com/klurosu/GoS-Klu/master/Tryndamere.Honest.lua<font color='#897676'>(thx Nodddy)</font>")
