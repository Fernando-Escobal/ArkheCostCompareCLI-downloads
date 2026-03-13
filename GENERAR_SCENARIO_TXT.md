# Generar scenario.txt

## Formato

```ini
[meta]
[simulation]
[rotations]
[team]
[selection]
[output]
```

## Ejemplo

```ini
[meta]
name=example_constellation_compare
version=1

[simulation]
iterations=100
rots=4
parallel=4
timeoutMs=120000
gcsimPath=
erIterations=350
erPercentile=0.8
enableER=false
substatOptim=false
gcsimVerbose=false

[rotations]
r1.label=rotation_a
r1.weight=50
r1.input=Nahida EQ > Xingqiu EQ > Yelan EQ > Kuki E

r2.label=rotation_b
r2.weight=50
r2.input=Yelan EQ > Nahida EQ > Xingqiu EQ > Kuki E

[team]
slot1.character=nahida
slot1.cons=0|2
slot1.weapon4=r5sacrificialfragments
slot1.weapon5=r1athousandfloatingdreams
slot1.set=4deepwood
slot1.main=edc

slot2.character=xingqiu
slot2.cons=6
slot2.weapon4=r5favoniussword
slot2.weapon5=
slot2.set=4emblem
slot2.main=adc

slot3.character=kuki
slot3.cons=0|2|4
slot3.weapon4=r5ironsting
slot3.weapon5=r1freedomsworn
slot3.set=4flowerofparadiselost
slot3.main=eec

slot4.character=yelan
slot4.cons=0|1
slot4.weapon4=r5favoniuswarbow
slot4.weapon5=r1elegyfortheend
slot4.set=4emblem
slot4.main=hdc

[selection]
liquidRolls=20,22,24
requireWeight100=true

[output]
csv=./out/results.csv
json=./out/results.json
sortBy=weightedDps
sortDir=desc
topN=0
keepTemp=false
tempDir=./.tmp
```

## Reglas clave

- gcsimPath es opcional.
- Si gcsimPath esta vacio, se intenta ../gcsim/gcsim.exe.
- Los pesos de [rotations] deben sumar 100 cuando requireWeight100=true.
- El equipo debe tener exactamente slot1..slot4.
- slotN.weapon4 es obligatorio.
- liquidRolls debe tener al menos un entero.
