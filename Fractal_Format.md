# Yedoma Globula Fractal Format
These definitions start at line 49 in `Data/Shaders/GLSL/fractal.glsl`. Each constant maps to a fractal type, used by each selectable fractal:

### ??: FCT_PROTEIN
```c
vec3 p = pos * 0.002; // <-- 3D Position
vec4 q = vec4(p - 1.0, 1); // <-- 4D Map onto Position

for (int i = 0; i < 7; i++) { <-- Primary Iteration
    q.xyz = abs(q.xyz + 1.3) - 1.0;
    q /= clamp(dot(q.xyz, q.xyz), 0.0, 0.8);
    q.xyz *= cFcRot;                         //  \
    q *= 1.567 + cFcParams.x;                //   | Lines present only
    q = q.zxyw;                              //   | in this loop
    q += vec4(0.2119, 0.8132, 0.077213, 0.); //  /
}

for (int i = 0; i < 4; i++) { // <-- Secondary iteration
    q.xyz = abs(q.xyz + 1.3) - 1.0;
    q /= clamp(dot(q.xyz, q.xyz), 0.0, 0.8);
    q *= 1.867;
}

dist = (length(q.xyz) - max(-240. - p.y * 700., 2.5)) / q.w * 300.; // <-- Distance
```

### Orbit Trap Fractal: FCT_ORBIT
```c
vec3 p = pos * 0.001;  // <-- 3D Position
vec4 q = vec4(p - 1.0, 1); // <-- 4D Map onto Position

for (int i = 0; i < 11; i++) { // <-- Iteration
    q.xyz = abs(q.xyz + 1.0) - 1.0;
    q.xyz = 2.0 * clamp(q.xyz, -73.0174, 73.0174) - q.xyz;
    q /= min(dot(q.xyz, q.xyz), 0.9);
    q *= 1.817; // + p.y*0.8;
}

tex = q.y; // <-- Texture Primary
tex2 = q.w; // <-- Texture Secondary
dist = (length(q.xz) - 2.1) / q.w * 800.; // <-- Distance
```

### ??: FCT_MNMT
```c
vec3 CSize = vec3(1., 1., 1.3); // <-- CSize Constant
vec3 p = pos.yzx; // <-- 3D Position
float scale = 1.0; // <-- Scale

for (int i = 0; i < 8; i++) { // <-- Primary iteration
    p = 2.0 * clamp(p, -CSize, CSize) - p;
    float r2 = dot(p, p);
    float k = max((2.) / (r2), .1274);
    p *= k;
    p *= cFcRot;                                    // \ Lines present only
    p.xyz = vec3(1.0 * p.z, 1.0 * p.x, -1.0 * p.y); // / in this loop
    scale *= k;
}

CSize = vec3(1.2, 0.4, 1.4); // <-- CSize Constant
tex = p.y; <-- Texture Primary

for (int i = 0; i < 4; i++) { // <-- Secondary Iteration
    p = 2.0 * clamp(p, -CSize, CSize) - p;
    float r2 = dot(p, p);
    float k = max((1.6) / (r2), 0.0274);
    p *= k;
    scale *= k;
}

float l = length(p.xyz);
float rxy = l - 1.4;
float n = 1.0 * p.z;
rxy = max(rxy, -(n) / 4.);
dist = (rxy) / abs(scale);

dist *= 1.5; // <-- Distance
```

### Crab Carpet Fractal: FCT_CRAB
```c
vec3 p = pos * 0.002; // <-- 3D Position
vec4 q = vec4(p - 1.0, 1); // <-- 4D Map onto Position

for (int i = 0; i < 7; i++) { // <-- Primary iteration
    q.xyz = abs(q.xyz + 1.3) - 1.0;
    q /= clamp(dot(q.xyz, q.xyz), 0.0, 0.8);
    q *= 1.867; // + p.y*0.8;
    q = q.zxyw;                              // \ Lines present only
    q += vec4(0.2119, 0.8132, 0.077213, 0.); // / in this loop
}

for (int i = 0; i < 4; i++) { // <-- Secondary iteration
    q.xyz = abs(q.xyz + 1.3) - 1.0;
    q /= clamp(dot(q.xyz, q.xyz), 0.0, 0.8);
    q *= 1.867; // + p.y*0.8;
}

dist = (length(q.yz) - 1.2) / q.w * 300.; // <-- Distance
```

### ??: FCT_BBSK
```c
vec3 CSize = vec3(1.4, 0.87, 1.1); // <-- CSize Constant
vec3 p = pos.xzy * 2.0; // <-- 3D Position
float scale = 1.0; // <-- Scale

for (int i = 0; i < 4; i++) { // <-- Primary iteration (These iterations are very different)
    p = 2.0 * clamp(p, -CSize, CSize) - p;
    float r2 = dot(p, p);
    float k = max((2.) / (r2), .17);
    p *= k;
    p += vec3(0.2, 0.2, -0.5);
    scale *= k;
}

tex2 = p.x; // <-- Texture Secondary

p = 2.0 * clamp(p, -CSize * 4., CSize * 4.) - p;

for (int i = 0; i < 8; i++) { // <-- Secondary iteration
    p = 2.0 * clamp(p, -CSize, CSize) - p;
    float r2 = dot(p, p);
    float k = max((cFcParams.x) / (r2), 0.027);
    p *= k;
    scale *= k;
    p.y += cFcParams.y;
}

float l = length(p.xy);
float rxy = l - 4.0;
float n = 1.0 * p.z;
rxy = max(rxy, -(n) / 4.);
dist = (rxy) / abs(scale);

tex = min(scale, 150.); // <-- Texture Primary
dist *= .75; // <-- Distance
```

### ??: FCT_HUB
```c
vec3 p = pos.yzx * 0.05; // <-- 3D Position
vec4 q = vec4(p - 1.0, 1); // <-- 4D Map onto Position

for (int i = 0; i < 8; i++) { // <-- Primary iteration (All lines shared)
    q.xyz = abs(q.xyz + cFcParams.z) - 1.0;
    q /= clamp(dot(q.xyz, q.xyz), 0.12, 1.0);
    q *= 1.0 + cFcParams.x;
    q.xyz *= cFcRot;
}

tex = q.x; // <-- Texture Primary

for (int i = 0; i < 2; i++) { // <-- Secondary iteration
    q.xyz = abs(q.xyz + cFcParams.z) - 1.0;
    q /= clamp(dot(q.xyz, q.xyz), 0.12, 1.0);
    q *= 1.0 + cFcParams.x;
    q.xyz *= cFcRot;
}

dist = (length(q.xyz) - 1. + cFcParams.y) / q.w * 19.; // <-- Distance
```

### Hyper Apophysis(?) Fractal: FCT_HYPERAPO
```c
vec3 CSize = vec3(1., 1., 1.1); // <-- CSize Constant
vec3 p = pos.yzx; // <-- 3D Position
float scale = 1.0; // <-- Scale

for (int i = 0; i < 4; i++) { // <-- Primary iteration
    p = 2.0 * clamp(p, -CSize, CSize) - p;
    float r2 = dot(p, p);
    float k = max((2.) / (r2), 0.067);
    p *= k;
    p.xyz = vec3(1.0 * p.z, 1.0 * p.x, -1.0 * p.y); // Line present only in this loop
    scale *= k;
}

p = p.zxy;
p *= cFcRot;

CSize = vec3(1.2, cFcParams.x, 1.2); // <-- CSize Constant
tex = p.y; // <-- Texture Primary

for (int i = 0; i < 7; i++) { // <-- Secondary iteration
    p = 2.0 * clamp(p, -CSize, CSize) - p;
    float r2 = dot(p, p);
    float k = max((1.6) / (r2), cFcParams.y);
    p *= k;
    scale *= k;
}

float l = length(p.xyz);
float rxy = l - 1.4;
float n = 1.0 * p.z;
rxy = max(rxy, -(n) / 4.);

dist = (rxy) / abs(scale); // <-- Distance
```

### ??: FCT_DLBT
```c
vec3 npos = pos;
float noise = noise3d(npos * 0.07) * 10.;
vec3 apopos = pos.xzy;
apopos.z += cFcParams.x;
domainRep3(apopos, vec3(250., 250., 0));
float r2 = 0.;
float k = 0.;

vec3 CSize = vec3(1., 1., 1.3); // <-- CSize Constant
vec3 p = apopos; // <-- 3D Position
float scale = 1.0; // <-- Scale

for (int i = 0; i < 5; i++) { // <-- Primary iteration (All lines shared)
    p = 2.0 * clamp(p, -CSize, CSize) - p;
    r2 = dot(p, p);
    k = max((2.0) / (r2), .0274);
    p *= k;
    scale *= k;

}

tex = scale; // <-- Texture Primary

for (int i = 0; i < 7; i++) { // <-- Secondary iteration
    p = 2.0 * clamp(p, -CSize, CSize) - p;
    r2 = dot(p, p);
    k = max((2.0) / (r2), .0274);
    p *= k;
    scale *= k;
}

float l = length(p.xy);
float rxy = l - 4.0;
float n = 1.0 * p.z;
rxy = max(rxy, -(n) / 4.);
float apodist = (rxy) / abs(scale);
apodist = 2. - apodist * 2.;
dist = noise - apodist + 0.01 * (pos.y - 67.);
dist *= 0.7;
dist = smin(dist, pos.y + cFcParams.y, 15.);

tex2 = p.z / scale; // <-- Texture Secondary
dist = smin(dist, pos.y - cFcParams.z, -15.); // <-- Distance
```

### ??: FCT_MZGN
```c
vec3 p = pos * 0.01; // <-- 3D Position
vec4 q = vec4(p, 1); // <-- 4D Map onto Position

vec4 qd;

for (int i = 0; i < 12; i++) { // <-- Primary iteration
    q.xyz = abs(q.xyz + 1.0 + cFcParams.y) - 1.0;
    qd = q;
    qd.w = qd.w * 0.002;
    q /= clamp(dot(qd, qd), 0.0, 0.8);
    q.xyz *= cFcRot;
    q *= 1.567 + cFcParams.x;
}

tex = q.w; // <-- Texture Primary
tex2 = q.x; // <-- Texture Secondary
dist = (length(q.xyz) - 3.5) / q.w * 100.; // <-- Distance
```

### Pipes Fractal: FCT_PIPES
```c
vec3 p = pos * 0.002; // <-- 3D Position
vec4 q = vec4(p - 1.0, 1); // <-- 4D Map onto Position

for (int i = 0; i < 11; i++) { // <-- Primary iteration
    q.xyz = abs(q.xyz + 1.0) - 1.0;
    q /= clamp(dot(q.xyz, q.xyz), 0.12, 1.0);
    q *= 1.837;
}

tex = q.y; // <-- Texture Primary
tex2 = q.w; // <-- Texture Secondary
dist = (length(q.xz) - 1.2) / q.w * 500.; // <-- Distance
```

### Apophysis(?) Fractal: FCT_APOP
```c
vec3 p = pos * 0.01; // <-- 3D Position
float scale = 1.0; // <-- Scale

for (int i = 0; i < 10; i++) { // <-- Primary iteration
    p = -1.0 + 2.0 * fract(0.5 * p + 0.5);
    float r2 = dot(p, p);
    float k = cFcParams.x / r2;
    p *= k;
    scale *= k;
    p *= cFcRot;
}

dist = length(p) / scale;

dist *= 25.; // <-- Distance
```

### Apophysis(?) Fractal: FCT_APO
```c
vec3 CSize = vec3(1.7, 1.7, 1.3); // <-- CSize Constant
vec3 p = pos.xzy; // <-- 3D Position
float scale = 1.; // <-- Scale

for (int i = 0; i < 12; i++) { // <-- Primary iteration
    p = 2.0 * clamp(p, -CSize, CSize) - p;
    float r2 = dot(p, p);
    float k = max((2.) / (r2), .027);
    p *= k;
    scale *= k;
}

float l = length(p.xyz);
float rxy = l - 4.0;
float n = l * p.z;
rxy = max(rxy, -(n) / 4.);
dist = (rxy) / abs(scale);

dist = max(dist, pos.x); // <-- Distance
```

### ??: FCT_HTVT
```c
vec3 CSize = vec3(1., 1., 1.3); // <-- CSize Constant
vec3 p = pos.yzx; // <-- 3D Position
float scale = 1.0 // <-- Scale

for (int i = 0; i < 4; i++) { // <-- Primary iteration
    p = 2.0 * clamp(p, -CSize, CSize) - p;
    float r2 = dot(p, p);
    float k = max((2.) / (r2), cFcParams.x);
    p *= k;
    p.xyz = vec3(1.0 * p.z, 1.0 * p.x, -1.0 * p.y); // Line present only in this loop
    scale *= k;
}

p = p.zxy;
p *= cFcRot;

CSize = vec3(1.2, 0.4, 1.4); // <-- CSize Constant
tex2 = p.x; // <-- Texture Secondary

for (int i = 0; i < 8; i++) { // <-- Secondary iteration
    p = 2.0 * clamp(p, -CSize, CSize) - p;
    float r2 = dot(p, p);
    float k = max((1.6) / (r2), cFcParams.y);
    p *= k;
    scale *= k;
}

float l = length(p.xyz);
float rxy = l - 1.4;
float n = 1.0 * p.z;
rxy = max(rxy, -(n) / 4.);
dist = (rxy) / abs(scale) - 0.0005;

tex = min(scale, 150.); // <-- Texture Primary
dist *= 1.4; // <-- Distance
```

### Knighty's Pseudo Kleinian Fractal: FCT_KNKL
```c
vec3 CSize = vec3(0.97478, 1.4202, 0.97478); // <-- CSize Constant
vec3 p = pos.xzy * 0.01; // <-- 3D Position

float DEfactor = 1.;
vec3 ap = p + 1.;

for (int i = 0; i < 11 && ap != p; i++) { // <-- Primary iteration
    ap = p;
    p = 2. * clamp(p, -CSize, CSize) - p;
    float r2 = dot(p, p);
    float k = max(1.0 / r2, 1.);
    p *= k;
    DEfactor *= k;
    p += C;
}

tex = p.y; // <-- Texture Primary
tex2 = DEfactor; // <-- Texture Secondary
dist = (0.5 * (p.z) / DEfactor) * 70; // <-- Distance
```

### KIFS Fractal: FCT_KIFS
```c
vec3 p = pos * 0.01; // <-- 3D Position

float l = 0.0;
vec3 Fold = vec3(3.);

for (int i = 0; i < 12; i++) { // <-- Primary iteration
    p.xyz = abs(p.xyz + Fold.xyz) - Fold.xyz;
    p = p * cFcParams.x;
    p *= cFcRot;
}

l = length(p);

dist = (l * pow(cFcParams.x, -12.) - 0.001) * 100.; // <-- Distance
```

### ??: FCT_TEXT
```c
vec3 p = pos; // <-- 3D Position

vec3 cell = domainRep3Idx(p, vec3(12., 8., 12.));
vec3 h = vec3(hash(cell.y), hash(cell.z + cell.x), hash(cell.x));
float rot = h.x + h.y + h.z * 6.3;
vec2 sc = vec2(sin(rot), cos(rot));
p.xz = vec2(sc.y * p.x - sc.x * p.z, sc.y * p.z + sc.x * p.x);
vec3 cube = abs(p) - vec3(3.);
cube *= 0.9;
dist = length(max(cube, 0.0)) - 1.5;
p += vec3(5.);
dist = max(dist, pos.y - 54.);
if (dist < 2.) {
    vec4 vol = texture3D(sVolumeMap, clamp(p.zxy * 0.1, 0., 1.)) * 0.9;
    float d = 2. * (vol.r + vol.g + vol.b);
    dist = smax(dist - 1.5, d * 1.0 - 0.1, 2.);
} else {
    dist += 1.;
}

dist = min(dist, pos.y + 4.); // <-- Distance
```

### ??: FCT_TEST
```c
vec3 pos1 = pos; // <-- 3D Position
vec3 pos2 = pos; // <-- 3D Position

domainRep3(pos1, vec3(45., 45., 45.));
domainRep3(pos2, vec3(5., 5., 5.));
dist = length(pos1) - 35.;

tex = dist; // <-- Texture Primary
tex2 = length(pos2); // <-- Texture Secondary

dist = smin(dist, tex2 - 5., -4.);

dist = min(dist, pos.y); // <-- Distance
```

Variables:
- `CSize Constant`: Size constant
- `3D Position`: Position
- `Scale`: Change size of the fractal
- `4D Map onto Position`: Not quite sure about this one
- `Texture Primary`: First texture
- `Texture Secondary`: Second texture
- `Distance`: Result of the signed distance function

The fractals here use what is known as [Distance Functions](https://iquilezles.org/www/articles/distfunctions/distfunctions.htm). Both [here](https://iquilezles.org/www/articles/mandelbulb/mandelbulb.htm) and [here](https://iquilezles.org/www/articles/distancefractals/distancefractals.htm) are useful resources for this. To find other resources, look up `Distance Estimated`.

## Scenes

The scenes listed in `Data\Scenes` each map to a name in the `load scene` screen. To open it, turn on `developer mode` and press `F4` while playing. The scene select screen will appear on the left. The ones in the directory are as follows:

```
APOP_rete.xml
APO_exp_apo.xml
BBSK_batagaika.xml
CRAB_crabtown.xml
DLBT_dlbt.xml
DLBT_dlbt_2.xml
FCT_MZGN_11109.xml
FCT_MZGN_36479.xml
FCT_MZGN_50959.xml
FCT_MZGN_61745.xml
FCT_MZGN_65579.xml
FCT_MZGN_66963.xml
FCT_MZGN_76897.xml
FCT_MZGN_88125.xml
FCT_MZGN_96615.xml
HTVT_htvt.xml
HTVT_swiss.xml
HUB_craters.xml
HUB_hub.xml
HYPERAPO_apo_1.xml
HYPERAPO_exp1.xml
HYPERAPO_signal.xml
KIFS_loop.xml
KNKL_knkl.xml
knkl_vrnk.xml
liana.xml
MNMT_mnmnt.xml
MZGN_fault.xml
MZGN_foldcube.xml
MZGN_ifsland.xml
MZGN_mezgine.xml
MZGN_ossium.xml
MZGN_pipulki.xml
MZGN_romanesco.xml
MZGN_thicket.xml
MZGN_zakrk.xml
MZGN_zgblk_3.xml
ORBIT_orbitals.xml
PIPES_kalipipes.xml
PROTEIN_protein.xml
test.xml
TEXT_3dtx.xml
```

From what I can deduce, each fractal file has the fractal type listed in the beginning of it's name, the type specified by the algorithms above, and the subtype is listed after an underscore.

An example file (`MZGN_ifsland.xml`), is displayed here:
```xml
<?xml version="1.0" encoding="UTF-8"?>
<scene id="1">
	<attribute name="Name" value="" />
	<attribute name="Time Scale" value="1" />
	<attribute name="Smoothing Constant" value="50" />
	<attribute name="Snap Threshold" value="5" />
	<attribute name="Elapsed Time" value="144.402" />
	<attribute name="Next Replicated Node ID" value="32" />
	<attribute name="Next Replicated Component ID" value="67" />
	<attribute name="Next Local Node ID" value="16777583" />
	<attribute name="Next Local Component ID" value="16779207" />
	<attribute name="Variables" />
	<attribute name="Variable Names" value="" />
	<component type="Octree" id="1" />
	<component type="DebugRenderer" id="2" />
	<component type="sdfManager" id="4" />
	<component type="fractalWorld" id="5">
		<attribute name="Fractal name" value="FCT_MZGN" /> <!-- Usually maps to the fractal constant -->
		<attribute name="Custom params" value="-0.39 0 0" />
		<attribute name="Rotation" value="29.68 37.1 -3.17" />
		<attribute name="Myst" value="-1.5" />
		<attribute name="Far clip" value="600" />
		<attribute name="Sky cubemap" value="TextureCube;Textures/cubemaps/bluecloud01.xml" />
		<attribute name="Additional sky color" value="0.03 0.04 0.03" />
		<attribute name="Sky luminocity" value="6" />
		<attribute name="Sky light intencity" value="4" />
		<attribute name="Texture pattern" value="Texture2D;Textures/patterns/ptrn1.png" />
		<attribute name="Texture colormap" value="Texture2D;Textures/colormaps/guts.png" />
		<attribute name="Pattern scale" value="13.57" />
		<attribute name="Colmap y-scale" value="999" />
		<attribute name="pattern influence" value="99" />
		<attribute name="fractal influence" value="199999" />
	</component>
	<!-- More objects -->
</scene>
```

## Info in YDG.xml

The scene is made selectable via the scene select dialog though its entry in `Data\Verses\YDG.xml`. An example of the format of each entry using the same `MZGN_ifsland.xml` from above:

```xml
<node id="7">
	<attribute name="Is Enabled" value="true" />
	<attribute name="Name" value="MZGN_ifsland.xml" />
	<attribute name="Tags" />
	<attribute name="Position" value="3.2407 1.48962 3.27386" />
	<attribute name="Rotation" value="1 0 0 0" />
	<attribute name="Scale" value="1 1 1" />
	<attribute name="Prefab" value="XMLFile;" />
	<attribute name="Variables" />
	<component type="ScriptInstance" id="13">
		<attribute name="Delayed Method Calls" value="0" />
		<attribute name="Script File" value="ScriptFile;Scripts/main.as" />
		<attribute name="Class Name" value="mmWorld" />
		<attribute name="mapFile" value="XMLFile;Scenes/MZGN_ifsland.xml" /> <!-- Scene file itself -->
		<attribute name="radius" value="1.61623" />
		<attribute name="maxRadius" value="3.23246" />
		<attribute name="defs" value="FCT_MZGN" /> <!-- Fractal type, IMPORTANT -->
		<attribute name="fc_params" value="-0.39 0 0" />
		<attribute name="fc_rot" value="29.68 37.1 -3.17" />
	</component>
	<node id="479">
		<attribute name="Is Enabled" value="true" />
		<attribute name="Name" value="pt" />
		<attribute name="Tags" />
		<attribute name="Position" value="-2.98183 -0.435778 -1.8068" />
		<attribute name="Rotation" value="1 0 0 0" />
		<attribute name="Scale" value="1 1 1" />
		<attribute name="Prefab" value="XMLFile;" />
		<attribute name="Variables" />
	</node>
</node>
```
