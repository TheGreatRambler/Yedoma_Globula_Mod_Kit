# Yedoma Globula Fractal Format
These definitions start at line 49 in `Data/Shaders/GLSL/fractal.glsl`. Each constant maps to a fractal type, used by each selectable fractal:

### ??: FCT_PROTEIN
```c
#ifdef FCT_PROTEIN

vec3 p = pos * 0.002;
vec4 q = vec4(p - 1., 1.);
for (int i = 0; i < 7; i++) {
    q.xyz = abs(q.xyz + 1.3) - 1.0;
    q /= clamp(dot(q.xyz, q.xyz), 0.0, 0.8);
    q.xyz *= cFcRot;
    q *= 1.567 + cFcParams.x; // + p.y*0.8;
    q = q.zxyw;
    q += vec4(0.2119, 0.8132, 0.077213, 0.);
}
for (int i = 0; i < 4; i++) {
    q.xyz = abs(q.xyz + 1.3) - 1.0;
    q /= clamp(dot(q.xyz, q.xyz), 0.0, 0.8);
    q *= 1.867; // + p.y*0.8;
}
dist = (length(q.xyz) - max(-240. - p.y * 700., 2.5)) / q.w * 300.;
```

### Orbit Trap Fractal: FCT_ORBIT
```c
#elif defined FCT_ORBIT

vec3 p = pos * 0.001;
vec4 q = vec4(p - 1.0, 1);
for (int i = 0; i < 11; i++) {
    //q.xyz = mod(q.xyz,2.0)-0.5*q.xyz;
    q.xyz = abs(q.xyz + 1.0) - 1.0;
    q.xyz = 2.0 * clamp(q.xyz, -73.0174, 73.0174) - q.xyz;
    q /= min(dot(q.xyz, q.xyz), 0.9);
    q *= 1.817; // + p.y*0.8;
    //q += vec4(0.2,.02,-.2,0.2);
    //q.xyz *= rot;
}
dist = (length(q.xz) - 2.1) / q.w * 800.;
tex = q.y;
tex2 = q.w;
```

### ??: FCT_MNMT
```c
#elif defined FCT_MNMT

vec3 CSize = vec3(1., 1., 1.3);
vec3 p = pos.yzx;
float scale = 1.0;
//float r2;
for (int i = 0; i < 8; i++) {
    p = 2.0 * clamp(p, -CSize, CSize) - p;
    float r2 = dot(p, p);
    //float r2 = dot(p,p+sin(p.z*.5)); //Alternate fractal
    float k = max((2.) / (r2), .1274);
    p *= k;
    p *= cFcRot;
    //p= p.yzx;
    p.xyz = vec3(1.0 * p.z, 1.0 * p.x, -1.0 * p.y);
    scale *= k;
}
tex = p.y;
CSize = vec3(1.2, 0.4, 1.4);
for (int i = 0; i < 4; i++) {
    p = 2.0 * clamp(p, -CSize, CSize) - p;
    float r2 = dot(p, p);
    //float r2 = dot(p,p+sin(p.z*.3)); //Alternate fractal
    float k = max((1.6) / (r2), 0.0274);
    p *= k;
    scale *= k;
}
float l = length(p.xyz);
float rxy = l - 1.4;
float n = 1.0 * p.z;
rxy = max(rxy, -(n) / 4.);
dist = (rxy) / abs(scale);
dist *= 1.5;
//tex = p.z;
```

### Crab Carpet Fractal: FCT_CRAB
```c
#elif defined FCT_CRAB

vec3 p = pos * 0.002;
//p*=cFcRot;
vec4 q = vec4(p - 1.0, 1);
for (int i = 0; i < 7; i++) {
    q.xyz = abs(q.xyz + 1.3) - 1.0;
    q /= clamp(dot(q.xyz, q.xyz), 0.0, 0.8);
    q *= 1.867; // + p.y*0.8;
    q = q.zxyw;
    q += vec4(0.2119, 0.8132, 0.077213, 0.);
}
for (int i = 0; i < 4; i++) {
    q.xyz = abs(q.xyz + 1.3) - 1.0;
    q /= clamp(dot(q.xyz, q.xyz), 0.0, 0.8);
    q *= 1.867; // + p.y*0.8;
}
dist = (length(q.yz) - 1.2) / q.w * 300.;
```

### ??: FCT_BBSK
```c
#elif defined FCT_BBSK

vec3 CSize = vec3(1.4, 0.87, 1.1);
vec3 p = pos.xzy * 2.0;
float scale = 1.0;
for (int i = 0; i < 4; i++) {
    p = 2.0 * clamp(p, -CSize, CSize) - p;
    float r2 = dot(p, p);
    //float r2 = dot(p,p+sin(p.z*.5)); //Alternate fractal
    float k = max((2.) / (r2), .17);
    p *= k;
    //p *=rot;
    //p= p.yzx;
    p += vec3(0.2, 0.2, -0.5);
    scale *= k;
}
tex2 = p.x;
p = 2.0 * clamp(p, -CSize * 4., CSize * 4.) - p;
for (int i = 0; i < 8; i++) {
    p = 2.0 * clamp(p, -CSize, CSize) - p;
    float r2 = dot(p, p);
    //float r2 = dot(p,p+sin(p.z*.3)); //Alternate fractal
    float k = max((cFcParams.x) / (r2), 0.027);
    p *= k;
    scale *= k;
    p.y += cFcParams.y;
}
float l = length(p.xy);
//l = mix(l,l2,0.5);
float rxy = l - 4.0;
float n = 1.0 * p.z;
rxy = max(rxy, -(n) / 4.);
dist = (rxy) / abs(scale);
dist *= .75;
tex = min(scale, 150.);
//tex = scale;
//dist = smin(dist,26 - length(pos),1.);
//dist = length(p2) - 2.;
//dist = max(dist,pos.x) ;
```

### ??: FCT_HUB
```c
#elif defined FCT_HUB

vec3 p = pos.yzx * 0.05;
vec4 q = vec4(p - 1.0, 1);
for (int i = 0; i < 8; i++) {
    q.xyz = abs(q.xyz + cFcParams.z) - 1.0;
    q /= clamp(dot(q.xyz, q.xyz), 0.12, 1.0);
    q *= 1.0 + cFcParams.x; // + p.y*0.8;
    q.xyz *= cFcRot;
}
tex = q.x;
for (int i = 0; i < 2; i++) {
    q.xyz = abs(q.xyz + cFcParams.z) - 1.0;
    q /= clamp(dot(q.xyz, q.xyz), 0.12, 1.0);
    q *= 1.0 + cFcParams.x; // + p.y*0.8;
    q.xyz *= cFcRot;
}
dist = (length(q.xyz) - 1. + cFcParams.y) / q.w * 19.;
```

### Hyper Apophysis(?) Fractal: FCT_HYPERAPO
```c
#elif defined FCT_HYPERAPO

vec3 CSize = vec3(1., 1., 1.1);
vec3 p = pos.yzx;
float scale = 1.0;
//float r2;
for (int i = 0; i < 4; i++) {
    p = 2.0 * clamp(p, -CSize, CSize) - p;
    float r2 = dot(p, p);
    //float r2 = dot(p,p+sin(p.z*.5)); //Alternate fractal
    float k = max((2.) / (r2), 0.067);
    p *= k;
    //p= p.yzx;
    p.xyz = vec3(1.0 * p.z, 1.0 * p.x, -1.0 * p.y);
    scale *= k;
}
p = p.zxy;
p *= cFcRot;
tex = p.y;
CSize = vec3(1.2, cFcParams.x, 1.2);
for (int i = 0; i < 7; i++) {
    p = 2.0 * clamp(p, -CSize, CSize) - p;
    float r2 = dot(p, p);
    //float r2 = dot(p,p+sin(p.z*.3)); //Alternate fractal
    float k = max((1.6) / (r2), cFcParams.y);
    p *= k;
    scale *= k;
}
float l = length(p.xyz);
float rxy = l - 1.4;
float n = 1.0 * p.z;
rxy = max(rxy, -(n) / 4.);
dist = (rxy) / abs(scale);
//dist *=1.2;
//tex = p.z;
```

### ??: FCT_DLBT
```c
#elif defined FCT_DLBT
vec3 npos = pos;
//npos.xz *= 0.6;
float noise = noise3d(npos * 0.07) * 10.;
vec3 apopos = pos.xzy;
apopos.z += cFcParams.x;
domainRep3(apopos, vec3(250., 250., 0));
//////////////////////////////////////
vec3 CSize = vec3(1., 1., 1.3);
vec3 p = apopos;
float scale = 1.0;
float r2 = 0.;
float k = 0.;
for (int i = 0; i < 5; i++) {
    p = 2.0 * clamp(p, -CSize, CSize) - p;
    r2 = dot(p, p);
    k = max((2.0) / (r2), .0274);
    p *= k;
    scale *= k;

}
//p *= cFcRot;
tex = scale;
for (int i = 0; i < 7; i++) {
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
//////////////////////////////////////
dist = noise - apodist + 0.01 * (pos.y - 67.);
dist *= 0.7;
dist = smin(dist, pos.y + cFcParams.y, 15.);
dist = smin(dist, pos.y - cFcParams.z, -15.);
tex2 = p.z / scale;
```

### ??: FCT_MZGN
```c
#elif defined FCT_MZGN

vec3 p = pos * 0.01;
vec4 q = vec4(p, 1);
vec4 qd;
for (int i = 0; i < 12; i++) {
    q.xyz = abs(q.xyz + 1.0 + cFcParams.y) - 1.0;
    qd = q;
    qd.w = qd.w * 0.002; // min(qd.w * 0.002,1.);
    q /= clamp(dot(qd, qd), 0.0, 0.8);
    q.xyz *= cFcRot;
    q *= 1.567 + cFcParams.x; // + p.y*0.8;
}
//q.y *= cFcParams.z;
dist = (length(q.xyz) - 3.5) / q.w * 100.;
//dist = smin(dist,pos.y,-2.);
tex = q.w;
tex2 = q.x;
```

### Pipes Fractal: FCT_PIPES
```c
#elif defined FCT_PIPES

vec3 p = pos * 0.002;
vec4 q = vec4(p - 1.0, 1);
for (int i = 0; i < 11; i++) {
    q.xyz = abs(q.xyz + 1.0) - 1.0;
    q /= clamp(dot(q.xyz, q.xyz), 0.12, 1.0);
    q *= 1.837; // + p.y*0.8;
}
dist = (length(q.xz) - 1.2) / q.w * 500.;
tex = q.y;
tex2 = q.w;
```

### Apophysis(?) Fractal: FCT_APOP
```c
#elif defined FCT_APOP

vec3 p = pos * 0.01;
float scale = 1.0;
for (int i = 0; i < 10; i++) {
    p = -1.0 + 2.0 * fract(0.5 * p + 0.5);
    float r2 = dot(p, p);
    float k = cFcParams.x / r2;
    p *= k;
    scale *= k;
    p *= cFcRot;
}
dist = length(p) / scale;
dist *= 25.;
```

### Apophysis(?) Fractal: FCT_APO
```c
#elif defined FCT_APO

vec3 p = pos.xzy;
vec3 CSize = vec3(1.7, 1.7, 1.3);
float scale = 1.;
for (int i = 0; i < 12; i++) {
    p = 2.0 * clamp(p, -CSize, CSize) - p;
    // p = -1.0 + 2.0*fract(0.5*p+0.5);
    float r2 = dot(p, p);
    //float r2 = dot(p,p+sin(p.z*.3)); //Alternate fractal
    float k = max((2.) / (r2), .027);
    //float k = (1.1)/(r2);
    p *= k;
    scale *= k;
    //p *= cFcRot;
    //p.z -= 0.02;
}
float l = length(p.xyz);
float rxy = l - 4.0;
float n = l * p.z;
rxy = max(rxy, -(n) / 4.);
dist = (rxy) / abs(scale);
//  dist= length(p.xyz)/scale - 0.1;
dist = max(dist, pos.x);
```

### ??: FCT_HTVT
```c
#elif defined FCT_HTVT

vec3 CSize = vec3(1., 1., 1.3);
vec3 p = pos.yzx;
float scale = 1.0;
//float r2;
for (int i = 0; i < 4; i++) {
    p = 2.0 * clamp(p, -CSize, CSize) - p;
    float r2 = dot(p, p);
    //float r2 = dot(p,p+sin(p.z*.5)); //Alternate fractal
    float k = max((2.) / (r2), cFcParams.x);
    p *= k;
    //p= p.yzx;
    p.xyz = vec3(1.0 * p.z, 1.0 * p.x, -1.0 * p.y);
    scale *= k;
}
p = p.zxy;
p *= cFcRot;
tex2 = p.x;
CSize = vec3(1.2, 0.4, 1.4);
for (int i = 0; i < 8; i++) {
    p = 2.0 * clamp(p, -CSize, CSize) - p;
    float r2 = dot(p, p);
    //float r2 = dot(p,p+sin(p.z*.3)); //Alternate fractal
    float k = max((1.6) / (r2), cFcParams.y);
    p *= k;
    scale *= k;
}
float l = length(p.xyz);
float rxy = l - 1.4;
float n = 1.0 * p.z;
rxy = max(rxy, -(n) / 4.);
dist = (rxy) / abs(scale) - 0.0005;
dist *= 1.4;
tex = min(scale, 150.);
```

### Pseudo Kleinian Fractal: FCT_KNKL
```c
#elif defined FCT_KNKL

//Knighty's Pseudo Kleinian from Fragmetarium
vec3 CSize = vec3(0.97478, 1.4202, 0.97478);
vec3 C = cFcParams; // vec3(-0.22972,0.04920,-0.29852);
vec3 p = pos.xzy * 0.01;
float DEfactor = 1.;
vec3 ap = p + 1.;
for (int i = 0; i < 11 && ap != p; i++) {
    ap = p;
    p = 2. * clamp(p, -CSize, CSize) - p;
    float r2 = dot(p, p);
    //orbitTrap = min(orbitTrap, abs(vec4(p,r2)));
    float k = max(1.0 / r2, 1.);
    p *= k;
    DEfactor *= k;
    p += C;
    //orbitTrap = min(orbitTrap, abs(vec4(p,dot(p,p))));
}
//Call basic shape and scale its DE
//return abs(0.5*Thingy(p,TThickness)/DEfactor-DEoffset);
//Alternative shape
//return abs(0.5*RoundBox(p, vec3(1.,1.,1.), 1.0)/DEfactor-DEoffset);
//Just a plane
tex = p.y;
tex2 = DEfactor;
dist = (0.5 * (p.z) / DEfactor) * 70;
```

### KIFS Fractal: FCT_KIFS
```c
#elif defined FCT_KIFS

float l = 0.0;
vec3 p = pos * 0.01;
vec3 Fold = vec3(3.);
for (int i = 0; i < 12; i++) {
    p.xyz = abs(p.xyz + Fold.xyz) - Fold.xyz;
    //p /= clamp(dot(p.xyz, p.xyz), 0.0, 1.0);
    p = p * cFcParams.x;
    p *= cFcRot;
}
l = length(p);
dist = (l * pow(cFcParams.x, -12.) - 0.001) * 100.;
```

### ??: FCT_TEXT
```c
#elif defined FCT_TEXT

vec3 p = pos;
vec3 cell = domainRep3Idx(p, vec3(12., 8., 12.));
vec3 h = vec3(hash(cell.y), hash(cell.z + cell.x), hash(cell.x));
float rot = h.x + h.y + h.z * 6.3;
vec2 sc = vec2(sin(rot), cos(rot));
p.xz = vec2(sc.y * p.x - sc.x * p.z, sc.y * p.z + sc.x * p.x);
//p+=h*8.;
vec3 cube = abs(p) - vec3(3.);
cube *= 0.9;
dist = length(max(cube, 0.0)) - 1.5;
p += vec3(5.);
dist = max(dist, pos.y - 54.);
//if (pos.y<56.)
if (dist < 2.) {
    vec4 vol = texture3D(sVolumeMap, clamp(p.zxy * 0.1, 0., 1.)) * 0.9;
    float d = 2. * (vol.r + vol.g + vol.b);
    dist = smax(dist - 1.5, d * 1.0 - 0.1, 2.);
} else {
    dist += 1.;
}
dist = min(dist, pos.y + 4.);
```

### ??: FCT_TEST
```c
#elif defined FCT_TEST

vec3 pos1 = pos;
vec3 pos2 = pos;
domainRep3(pos1, vec3(45., 45., 45.));
domainRep3(pos2, vec3(5., 5., 5.));
dist = length(pos1) - 35.;
tex = dist;
tex2 = length(pos2);
dist = smin(dist, tex2 - 5., -4.);
dist = min(dist, pos.y);
```

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
		<attribute name="Fractal name" value="FCT_MZGN" />
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
	<component type="PhysicsWorld" id="11" />
	<node id="16777219">
		<attribute name="Is Enabled" value="true" />
		<attribute name="Name" value="" />
		<attribute name="Tags" />
		<attribute name="Position" value="-30.5235 -6.6198 -161.968" />
		<attribute name="Rotation" value="1 0 0 0" />
		<attribute name="Scale" value="1 1 1" />
		<attribute name="Variables" />
		<component type="Light" id="16777240">
			<attribute name="Color" value="4 3.2 2 1" />
			<attribute name="Brightness Multiplier" value="3" />
			<attribute name="Range" value="71.34" />
			<attribute name="Spot FOV" value="41.69" />
			<attribute name="Light Shape Texture" value="TextureCube;" />
		</component>
	</node>
	<node id="16777233">
		<attribute name="Is Enabled" value="true" />
		<attribute name="Name" value="" />
		<attribute name="Tags" />
		<attribute name="Position" value="-12.4868 -17.3665 -7.43242" />
		<attribute name="Rotation" value="1 0 0 0" />
		<attribute name="Scale" value="1 1 1" />
		<attribute name="Variables" />
		<component type="Light" id="16777397">
			<attribute name="Color" value="1 0.75 0.54 1" />
			<attribute name="Range" value="50" />
			<attribute name="Spot FOV" value="41.69" />
			<attribute name="Light Shape Texture" value="TextureCube;" />
		</component>
	</node>
	<node id="16777234">
		<attribute name="Is Enabled" value="true" />
		<attribute name="Name" value="" />
		<attribute name="Tags" />
		<attribute name="Position" value="48.6058 -155.26 -206.853" />
		<attribute name="Rotation" value="1 0 0 0" />
		<attribute name="Scale" value="1 1 1" />
		<attribute name="Variables" />
		<component type="Light" id="16777398">
			<attribute name="Color" value="0.68 0.96 0.72 1" />
			<attribute name="Range" value="50" />
			<attribute name="Spot FOV" value="41.69" />
			<attribute name="Light Shape Texture" value="TextureCube;" />
		</component>
	</node>
	<node id="16777236">
		<attribute name="Is Enabled" value="true" />
		<attribute name="Name" value="" />
		<attribute name="Tags" />
		<attribute name="Position" value="-83.5606 -22.6344 -285.776" />
		<attribute name="Rotation" value="1 0 0 0" />
		<attribute name="Scale" value="1 1 1" />
		<attribute name="Variables" />
		<component type="Light" id="16777411">
			<attribute name="Color" value="0.68 0.42 0.99 1" />
			<attribute name="Range" value="50" />
			<attribute name="Spot FOV" value="41.69" />
			<attribute name="Light Shape Texture" value="TextureCube;" />
		</component>
	</node>
	<node id="16777237">
		<attribute name="Is Enabled" value="true" />
		<attribute name="Name" value="" />
		<attribute name="Tags" />
		<attribute name="Position" value="-56.8292 21.7079 21.5102" />
		<attribute name="Rotation" value="1 0 0 0" />
		<attribute name="Scale" value="1 1 1" />
		<attribute name="Variables" />
		<component type="Light" id="16777412">
			<attribute name="Color" value="0.73 0.78 0.99 1" />
			<attribute name="Range" value="50" />
			<attribute name="Spot FOV" value="41.69" />
			<attribute name="Light Shape Texture" value="TextureCube;" />
		</component>
	</node>
	<node id="16777253">
		<attribute name="Is Enabled" value="true" />
		<attribute name="Name" value="" />
		<attribute name="Tags" />
		<attribute name="Position" value="51.8581 17.8229 -33.9003" />
		<attribute name="Rotation" value="1 0 0 0" />
		<attribute name="Scale" value="1 1 1" />
		<attribute name="Variables" />
	</node>
	<node id="16777405">
		<attribute name="Is Enabled" value="true" />
		<attribute name="Name" value="PlayerStart" />
		<attribute name="Tags" />
		<attribute name="Position" value="-68.7394 13.4816 42.5632" />
		<attribute name="Rotation" value="0.707107 0 -0.707107 0" />
		<attribute name="Scale" value="1 1 1" />
		<attribute name="Variables" />
	</node>
	<node id="16777454">
		<attribute name="Is Enabled" value="true" />
		<attribute name="Name" value="base" />
		<attribute name="Tags" />
		<attribute name="Position" value="-71.4698 -13.1074 -26.3902" />
		<attribute name="Rotation" value="1 0 0 0" />
		<attribute name="Scale" value="1 1 1" />
		<attribute name="Variables" />
	</node>
	<node id="16777455">
		<attribute name="Is Enabled" value="true" />
		<attribute name="Name" value="platform1x2" />
		<attribute name="Tags" />
		<attribute name="Position" value="-71.4698 -13.1074 -26.3902" />
		<attribute name="Rotation" value="1 0 0 0" />
		<attribute name="Scale" value="1 1 1" />
		<attribute name="Variables" />
		<component type="StaticModel" id="16778602">
			<attribute name="Model" value="Model;Models/props/platform1x2.mdl" />
			<attribute name="Material" value="Material;Materials/props/plain.xml;Materials/props/trim.xml" />
		</component>
		<component type="RigidBody" id="16778603">
			<attribute name="Physics Position" value="-71.4698 -13.1074 -26.3902" />
			<attribute name="Collision Layer" value="2" />
			<attribute name="Use Gravity" value="false" />
		</component>
		<component type="CollisionShape" id="16778604">
			<attribute name="Shape Type" value="TriangleMesh" />
			<attribute name="Model" value="Model;Models/props/platform1x2.mdl" />
		</component>
	</node>
	<node id="16777456">
		<attribute name="Is Enabled" value="true" />
		<attribute name="Name" value="container" />
		<attribute name="Tags" />
		<attribute name="Position" value="-73.138 -13.1074 -26.1755" />
		<attribute name="Rotation" value="0.707107 0 0.707107 0" />
		<attribute name="Scale" value="1 1 1" />
		<attribute name="Variables" />
		<component type="StaticModel" id="16778605">
			<attribute name="Model" value="Model;Models/props/container.mdl" />
			<attribute name="Material" value="Material;Materials/props/trim.xml;Materials/props/plain.xml" />
		</component>
		<component type="RigidBody" id="16778606">
			<attribute name="Physics Rotation" value="0.707107 0 0.707107 0" />
			<attribute name="Physics Position" value="-73.138 -13.1074 -26.1755" />
			<attribute name="Collision Layer" value="2" />
			<attribute name="Use Gravity" value="false" />
		</component>
		<component type="CollisionShape" id="16778607">
			<attribute name="Shape Type" value="TriangleMesh" />
			<attribute name="Model" value="Model;Models/props/container.mdl" />
		</component>
	</node>
	<node id="16777457">
		<attribute name="Is Enabled" value="true" />
		<attribute name="Name" value="platform2x2" />
		<attribute name="Tags" />
		<attribute name="Position" value="-71.4698 -13.1074 -19.1418" />
		<attribute name="Rotation" value="1 0 0 0" />
		<attribute name="Scale" value="1 1 1" />
		<attribute name="Variables" />
		<component type="StaticModel" id="16778608">
			<attribute name="Model" value="Model;Models/props/platform2x2.mdl" />
			<attribute name="Material" value="Material;Materials/props/plain.xml;Materials/props/trim.xml" />
		</component>
		<component type="RigidBody" id="16778609">
			<attribute name="Physics Position" value="-71.4698 -13.1074 -19.1418" />
			<attribute name="Collision Layer" value="2" />
			<attribute name="Use Gravity" value="false" />
		</component>
		<component type="CollisionShape" id="16778610">
			<attribute name="Shape Type" value="TriangleMesh" />
			<attribute name="Model" value="Model;Models/props/platform2x2.mdl" />
		</component>
	</node>
	<node id="16777459">
		<attribute name="Is Enabled" value="true" />
		<attribute name="Name" value="base" />
		<attribute name="Tags" />
		<attribute name="Position" value="-71.4698 -21.8094 -2.07688" />
		<attribute name="Rotation" value="1 0 0 0" />
		<attribute name="Scale" value="1 1 1" />
		<attribute name="Variables" />
	</node>
	<node id="16777460">
		<attribute name="Is Enabled" value="true" />
		<attribute name="Name" value="platform1x2" />
		<attribute name="Tags" />
		<attribute name="Position" value="-83.2613 -31.8236 35.2448" />
		<attribute name="Rotation" value="1 0 0 0" />
		<attribute name="Scale" value="1 1 1" />
		<attribute name="Variables" />
		<component type="StaticModel" id="16778623">
			<attribute name="Model" value="Model;Models/props/platform1x2.mdl" />
			<attribute name="Material" value="Material;Materials/props/plain.xml;Materials/props/trim.xml" />
		</component>
		<component type="RigidBody" id="16778624">
			<attribute name="Physics Position" value="-83.2613 -31.8236 35.2448" />
			<attribute name="Collision Layer" value="2" />
			<attribute name="Use Gravity" value="false" />
		</component>
		<component type="CollisionShape" id="16778625">
			<attribute name="Shape Type" value="TriangleMesh" />
			<attribute name="Model" value="Model;Models/props/platform1x2.mdl" />
		</component>
	</node>
	<node id="16777461">
		<attribute name="Is Enabled" value="true" />
		<attribute name="Name" value="container" />
		<attribute name="Tags" />
		<attribute name="Position" value="-89.0187 -30.1332 -12.4074" />
		<attribute name="Rotation" value="0.707107 0 0.707107 0" />
		<attribute name="Scale" value="1 1 1" />
		<attribute name="Variables" />
		<component type="StaticModel" id="16778626">
			<attribute name="Model" value="Model;Models/props/container.mdl" />
			<attribute name="Material" value="Material;Materials/props/trim.xml;Materials/props/plain.xml" />
		</component>
		<component type="RigidBody" id="16778627">
			<attribute name="Physics Rotation" value="0.707107 0 0.707107 0" />
			<attribute name="Physics Position" value="-89.0187 -30.1332 -12.4074" />
			<attribute name="Collision Layer" value="2" />
			<attribute name="Use Gravity" value="false" />
		</component>
		<component type="CollisionShape" id="16778628">
			<attribute name="Shape Type" value="TriangleMesh" />
			<attribute name="Model" value="Model;Models/props/container.mdl" />
		</component>
	</node>
	<node id="16777462">
		<attribute name="Is Enabled" value="true" />
		<attribute name="Name" value="platform2x2" />
		<attribute name="Tags" />
		<attribute name="Position" value="-82.8219 0.934 7.36275" />
		<attribute name="Rotation" value="1 0 0 0" />
		<attribute name="Scale" value="1 1 1" />
		<attribute name="Variables" />
		<component type="StaticModel" id="16778629">
			<attribute name="Model" value="Model;Models/props/platform2x2.mdl" />
			<attribute name="Material" value="Material;Materials/props/plain.xml;Materials/props/trim.xml" />
		</component>
		<component type="RigidBody" id="16778630">
			<attribute name="Physics Position" value="-82.8219 0.934 7.36275" />
			<attribute name="Collision Layer" value="2" />
			<attribute name="Use Gravity" value="false" />
		</component>
		<component type="CollisionShape" id="16778631">
			<attribute name="Shape Type" value="TriangleMesh" />
			<attribute name="Model" value="Model;Models/props/platform2x2.mdl" />
		</component>
	</node>
	<node id="16777467">
		<attribute name="Is Enabled" value="true" />
		<attribute name="Name" value="vent_barrel" />
		<attribute name="Tags" />
		<attribute name="Position" value="-80.6723 -12.1243 -18.2742" />
		<attribute name="Rotation" value="1 0 0 0" />
		<attribute name="Scale" value="1 1 1" />
		<attribute name="Variables" />
		<component type="StaticModel" id="16778650">
			<attribute name="Model" value="Model;Models/props/vent_barrel.mdl" />
			<attribute name="Material" value="Material;Materials/props/trim.xml" />
		</component>
		<component type="RigidBody" id="16778651">
			<attribute name="Physics Position" value="-80.6723 -12.1243 -18.2742" />
			<attribute name="Collision Layer" value="2" />
			<attribute name="Use Gravity" value="false" />
		</component>
		<component type="CollisionShape" id="16778652">
			<attribute name="Shape Type" value="TriangleMesh" />
			<attribute name="Model" value="Model;Models/props/vent_barrel.mdl" />
		</component>
	</node>
	<node id="16777469">
		<attribute name="Is Enabled" value="true" />
		<attribute name="Name" value="vent_barrel" />
		<attribute name="Tags" />
		<attribute name="Position" value="-91.6767 -21.8559 10.1385" />
		<attribute name="Rotation" value="-4.37114e-008 0 1 0" />
		<attribute name="Scale" value="1 1 1" />
		<attribute name="Variables" />
		<component type="StaticModel" id="16778656">
			<attribute name="Model" value="Model;Models/props/vent_barrel.mdl" />
			<attribute name="Material" value="Material;Materials/props/trim.xml" />
		</component>
		<component type="RigidBody" id="16778657">
			<attribute name="Physics Rotation" value="-4.37114e-008 0 1 0" />
			<attribute name="Physics Position" value="-91.6767 -21.8559 10.1385" />
			<attribute name="Collision Layer" value="2" />
			<attribute name="Use Gravity" value="false" />
		</component>
		<component type="CollisionShape" id="16778658">
			<attribute name="Shape Type" value="TriangleMesh" />
			<attribute name="Model" value="Model;Models/props/vent_barrel.mdl" />
		</component>
	</node>
	<node id="16777516">
		<attribute name="Is Enabled" value="true" />
		<attribute name="Name" value="" />
		<attribute name="Tags" />
		<attribute name="Position" value="-93.1416 -30.1037 -53.744" />
		<attribute name="Rotation" value="1 0 0 0" />
		<attribute name="Scale" value="1 1 1" />
		<attribute name="Variables" />
		<node id="29">
			<attribute name="Is Enabled" value="true" />
			<attribute name="Name" value="base" />
			<attribute name="Tags" />
			<attribute name="Position" value="0.807106 -1.75357 -1.51097" />
			<attribute name="Rotation" value="1 0 0 0" />
			<attribute name="Scale" value="1 1 1" />
			<attribute name="Variables" />
			<node id="16777517">
				<attribute name="Is Enabled" value="true" />
				<attribute name="Name" value="doppler" />
				<attribute name="Tags" />
				<attribute name="Position" value="-8.5 -0.5 3.5" />
				<attribute name="Rotation" value="1 0 0 0" />
				<attribute name="Scale" value="1 1 1" />
				<attribute name="Variables" />
				<component type="StaticModel" id="16778829">
					<attribute name="Model" value="Model;Models/props/doppler.mdl" />
					<attribute name="Material" value="Material;Materials/props/plain.xml;Materials/props/trim.xml" />
				</component>
				<component type="RigidBody" id="16778830">
					<attribute name="Physics Position" value="-100.834 -32.3573 -51.755" />
					<attribute name="Collision Layer" value="2" />
					<attribute name="Use Gravity" value="false" />
				</component>
				<component type="CollisionShape" id="16778831">
					<attribute name="Shape Type" value="TriangleMesh" />
					<attribute name="Model" value="Model;Models/props/doppler.mdl" />
				</component>
			</node>
			<node id="16777519">
				<attribute name="Is Enabled" value="true" />
				<attribute name="Name" value="platform1x2" />
				<attribute name="Tags" />
				<attribute name="Position" value="-2 0 17.5" />
				<attribute name="Rotation" value="1 0 0 0" />
				<attribute name="Scale" value="1 1 1" />
				<attribute name="Variables" />
				<component type="StaticModel" id="16778835">
					<attribute name="Model" value="Model;Models/props/platform1x2.mdl" />
					<attribute name="Material" value="Material;Materials/props/plain.xml;Materials/props/trim.xml" />
				</component>
				<component type="RigidBody" id="16778836">
					<attribute name="Physics Position" value="-94.3345 -31.8573 -37.755" />
					<attribute name="Collision Layer" value="2" />
					<attribute name="Use Gravity" value="false" />
				</component>
				<component type="CollisionShape" id="16778837">
					<attribute name="Shape Type" value="TriangleMesh" />
					<attribute name="Model" value="Model;Models/props/platform1x2.mdl" />
				</component>
			</node>
			<node id="16777520">
				<attribute name="Is Enabled" value="true" />
				<attribute name="Name" value="pier" />
				<attribute name="Tags" />
				<attribute name="Position" value="-30 0 0" />
				<attribute name="Rotation" value="1 0 0 0" />
				<attribute name="Scale" value="1 1 1" />
				<attribute name="Variables" />
				<component type="StaticModel" id="16778838">
					<attribute name="Model" value="Model;Models/props/pier.mdl" />
					<attribute name="Material" value="Material;Materials/props/plain.xml;Materials/props/trim.xml" />
				</component>
				<component type="RigidBody" id="16778839">
					<attribute name="Physics Position" value="-122.334 -31.8573 -55.255" />
					<attribute name="Collision Layer" value="2" />
					<attribute name="Use Gravity" value="false" />
				</component>
				<component type="CollisionShape" id="16778840">
					<attribute name="Shape Type" value="TriangleMesh" />
					<attribute name="Model" value="Model;Models/props/pier.mdl" />
				</component>
			</node>
			<node id="16777521">
				<attribute name="Is Enabled" value="true" />
				<attribute name="Name" value="platform2x2" />
				<attribute name="Tags" />
				<attribute name="Position" value="0 0 7.5" />
				<attribute name="Rotation" value="1 0 0 0" />
				<attribute name="Scale" value="1 1 1" />
				<attribute name="Variables" />
				<component type="StaticModel" id="16778841">
					<attribute name="Model" value="Model;Models/props/platform2x2.mdl" />
					<attribute name="Material" value="Material;Materials/props/plain.xml;Materials/props/trim.xml" />
				</component>
				<component type="RigidBody" id="16778842">
					<attribute name="Physics Position" value="-92.3345 -31.8573 -47.755" />
					<attribute name="Collision Layer" value="2" />
					<attribute name="Use Gravity" value="false" />
				</component>
				<component type="CollisionShape" id="16778843">
					<attribute name="Shape Type" value="TriangleMesh" />
					<attribute name="Model" value="Model;Models/props/platform2x2.mdl" />
				</component>
			</node>
			<node id="16777522">
				<attribute name="Is Enabled" value="true" />
				<attribute name="Name" value="slope1x2" />
				<attribute name="Tags" />
				<attribute name="Position" value="-22 -5 22.5" />
				<attribute name="Rotation" value="-3.35276e-008 0 1 0" />
				<attribute name="Scale" value="1 1 1" />
				<attribute name="Variables" />
				<component type="StaticModel" id="16778844">
					<attribute name="Model" value="Model;Models/props/slope1x2.mdl" />
					<attribute name="Material" value="Material;Materials/props/plain.xml;Materials/props/trim.xml" />
				</component>
				<component type="RigidBody" id="16778845">
					<attribute name="Physics Rotation" value="-3.35276e-008 0 1 0" />
					<attribute name="Physics Position" value="-114.334 -36.8573 -32.755" />
					<attribute name="Collision Layer" value="2" />
					<attribute name="Use Gravity" value="false" />
				</component>
				<component type="CollisionShape" id="16778846">
					<attribute name="Shape Type" value="TriangleMesh" />
					<attribute name="Model" value="Model;Models/props/slope1x2.mdl" />
				</component>
			</node>
			<node id="16777523">
				<attribute name="Is Enabled" value="true" />
				<attribute name="Name" value="container" />
				<attribute name="Tags" />
				<attribute name="Position" value="16.5 -4 12.5" />
				<attribute name="Rotation" value="0.707107 0 0.707107 0" />
				<attribute name="Scale" value="1 1 1" />
				<attribute name="Variables" />
				<component type="StaticModel" id="16778847">
					<attribute name="Model" value="Model;Models/props/container.mdl" />
					<attribute name="Material" value="Material;Materials/props/trim.xml;Materials/props/plain.xml" />
				</component>
				<component type="RigidBody" id="16778848">
					<attribute name="Physics Rotation" value="0.707107 0 0.707107 0" />
					<attribute name="Physics Position" value="-75.8345 -35.8573 -42.755" />
					<attribute name="Collision Layer" value="2" />
					<attribute name="Use Gravity" value="false" />
				</component>
				<component type="CollisionShape" id="16778849">
					<attribute name="Shape Type" value="TriangleMesh" />
					<attribute name="Model" value="Model;Models/props/container.mdl" />
				</component>
			</node>
			<node id="16777524">
				<attribute name="Is Enabled" value="true" />
				<attribute name="Name" value="platform1x2" />
				<attribute name="Tags" />
				<attribute name="Position" value="5 0 -7.5" />
				<attribute name="Rotation" value="1 0 0 0" />
				<attribute name="Scale" value="1 1 1" />
				<attribute name="Variables" />
				<component type="StaticModel" id="16778850">
					<attribute name="Model" value="Model;Models/props/platform1x2.mdl" />
					<attribute name="Material" value="Material;Materials/props/plain.xml;Materials/props/trim.xml" />
				</component>
				<component type="RigidBody" id="16778851">
					<attribute name="Physics Position" value="-87.3345 -31.8573 -62.755" />
					<attribute name="Collision Layer" value="2" />
					<attribute name="Use Gravity" value="false" />
				</component>
				<component type="CollisionShape" id="16778852">
					<attribute name="Shape Type" value="TriangleMesh" />
					<attribute name="Model" value="Model;Models/props/platform1x2.mdl" />
				</component>
			</node>
		</node>
		<node id="30">
			<attribute name="Is Enabled" value="true" />
			<attribute name="Name" value="base" />
			<attribute name="Tags" />
			<attribute name="Position" value="0.807106 -1.75357 -1.51097" />
			<attribute name="Rotation" value="1 0 0 0" />
			<attribute name="Scale" value="1 1 1" />
			<attribute name="Variables" />
			<node id="16777527">
				<attribute name="Is Enabled" value="true" />
				<attribute name="Name" value="platform1x2" />
				<attribute name="Tags" />
				<attribute name="Position" value="-5 -20 5" />
				<attribute name="Rotation" value="0.707107 0 0.707107 0" />
				<attribute name="Scale" value="1 1 1" />
				<attribute name="Variables" />
				<component type="StaticModel" id="16778865">
					<attribute name="Model" value="Model;Models/props/platform1x2.mdl" />
					<attribute name="Material" value="Material;Materials/props/plain.xml;Materials/props/trim.xml" />
				</component>
				<component type="RigidBody" id="16778866">
					<attribute name="Physics Rotation" value="0.707107 0 0.707107 0" />
					<attribute name="Physics Position" value="-97.3345 -51.8573 -50.255" />
					<attribute name="Collision Layer" value="2" />
					<attribute name="Use Gravity" value="false" />
				</component>
				<component type="CollisionShape" id="16778867">
					<attribute name="Shape Type" value="TriangleMesh" />
					<attribute name="Model" value="Model;Models/props/platform1x2.mdl" />
				</component>
			</node>
			<node id="16777528">
				<attribute name="Is Enabled" value="true" />
				<attribute name="Name" value="platform2x2" />
				<attribute name="Tags" />
				<attribute name="Position" value="5 -20 15" />
				<attribute name="Rotation" value="1 0 0 0" />
				<attribute name="Scale" value="1 1 1" />
				<attribute name="Variables" />
				<component type="StaticModel" id="16778868">
					<attribute name="Model" value="Model;Models/props/platform2x2.mdl" />
					<attribute name="Material" value="Material;Materials/props/plain.xml;Materials/props/trim.xml" />
				</component>
				<component type="RigidBody" id="16778869">
					<attribute name="Physics Position" value="-87.3345 -51.8573 -40.255" />
					<attribute name="Collision Layer" value="2" />
					<attribute name="Use Gravity" value="false" />
				</component>
				<component type="CollisionShape" id="16778870">
					<attribute name="Shape Type" value="TriangleMesh" />
					<attribute name="Model" value="Model;Models/props/platform2x2.mdl" />
				</component>
			</node>
			<node id="16777529">
				<attribute name="Is Enabled" value="true" />
				<attribute name="Name" value="pier" />
				<attribute name="Tags" />
				<attribute name="Position" value="0 0 2.5" />
				<attribute name="Rotation" value="1 0 0 0" />
				<attribute name="Scale" value="1 1 1" />
				<attribute name="Variables" />
				<component type="StaticModel" id="16778871">
					<attribute name="Model" value="Model;Models/props/pier.mdl" />
					<attribute name="Material" value="Material;Materials/props/plain.xml;Materials/props/trim.xml" />
				</component>
				<component type="RigidBody" id="16778872">
					<attribute name="Physics Position" value="-92.3345 -31.8573 -52.755" />
					<attribute name="Collision Layer" value="2" />
					<attribute name="Use Gravity" value="false" />
				</component>
				<component type="CollisionShape" id="16778873">
					<attribute name="Shape Type" value="TriangleMesh" />
					<attribute name="Model" value="Model;Models/props/pier.mdl" />
				</component>
			</node>
			<node id="16777531">
				<attribute name="Is Enabled" value="true" />
				<attribute name="Name" value="Plane" />
				<attribute name="Tags" />
				<attribute name="Position" value="16.5 -4 11" />
				<attribute name="Rotation" value="1 0 0 0" />
				<attribute name="Scale" value="1 1 1" />
				<attribute name="Variables" />
				<component type="StaticModel" id="16778877">
					<attribute name="Model" value="Model;Models/props/Plane.mdl" />
					<attribute name="Material" value="Material;Materials/props/trim.xml" />
				</component>
				<component type="RigidBody" id="16778878">
					<attribute name="Physics Position" value="-75.8345 -35.8573 -44.255" />
					<attribute name="Collision Layer" value="2" />
					<attribute name="Use Gravity" value="false" />
				</component>
				<component type="CollisionShape" id="16778879">
					<attribute name="Shape Type" value="TriangleMesh" />
					<attribute name="Model" value="Model;Models/props/Plane.mdl" />
				</component>
			</node>
			<node id="16777532">
				<attribute name="Is Enabled" value="true" />
				<attribute name="Name" value="container" />
				<attribute name="Tags" />
				<attribute name="Position" value="23 0 3.5" />
				<attribute name="Rotation" value="1 0 0 0" />
				<attribute name="Scale" value="1 1 1" />
				<attribute name="Variables" />
				<component type="StaticModel" id="16778880">
					<attribute name="Model" value="Model;Models/props/container.mdl" />
					<attribute name="Material" value="Material;Materials/props/trim.xml;Materials/props/plain.xml" />
				</component>
				<component type="RigidBody" id="16778881">
					<attribute name="Physics Position" value="-69.3345 -31.8573 -51.755" />
					<attribute name="Collision Layer" value="2" />
					<attribute name="Use Gravity" value="false" />
				</component>
				<component type="CollisionShape" id="16778882">
					<attribute name="Shape Type" value="TriangleMesh" />
					<attribute name="Model" value="Model;Models/props/container.mdl" />
				</component>
			</node>
			<node id="16777533">
				<attribute name="Is Enabled" value="true" />
				<attribute name="Name" value="slope1x2" />
				<attribute name="Tags" />
				<attribute name="Position" value="0 -20 10" />
				<attribute name="Rotation" value="-4.37114e-008 0 1 -0" />
				<attribute name="Scale" value="1 1 1" />
				<attribute name="Variables" />
				<component type="StaticModel" id="16778883">
					<attribute name="Model" value="Model;Models/props/slope1x2.mdl" />
					<attribute name="Material" value="Material;Materials/props/plain.xml;Materials/props/trim.xml" />
				</component>
				<component type="RigidBody" id="16778884">
					<attribute name="Physics Rotation" value="-4.37114e-008 0 1 0" />
					<attribute name="Physics Position" value="-92.3345 -51.8573 -45.255" />
					<attribute name="Collision Layer" value="2" />
					<attribute name="Use Gravity" value="false" />
				</component>
				<component type="CollisionShape" id="16778885">
					<attribute name="Shape Type" value="TriangleMesh" />
					<attribute name="Model" value="Model;Models/props/slope1x2.mdl" />
				</component>
			</node>
			<node id="16777534">
				<attribute name="Is Enabled" value="true" />
				<attribute name="Name" value="Plane" />
				<attribute name="Tags" />
				<attribute name="Position" value="-27 -2 1" />
				<attribute name="Rotation" value="1 0 0 0" />
				<attribute name="Scale" value="1 1 1" />
				<attribute name="Variables" />
				<component type="StaticModel" id="16778886">
					<attribute name="Model" value="Model;Models/props/Plane.mdl" />
					<attribute name="Material" value="Material;Materials/props/trim.xml" />
				</component>
				<component type="RigidBody" id="16778887">
					<attribute name="Physics Position" value="-119.334 -33.8573 -54.255" />
					<attribute name="Collision Layer" value="2" />
					<attribute name="Use Gravity" value="false" />
				</component>
				<component type="CollisionShape" id="16778888">
					<attribute name="Shape Type" value="TriangleMesh" />
					<attribute name="Model" value="Model;Models/props/Plane.mdl" />
				</component>
			</node>
			<node id="16777535">
				<attribute name="Is Enabled" value="true" />
				<attribute name="Name" value="slope1x2" />
				<attribute name="Tags" />
				<attribute name="Position" value="10 -15 10" />
				<attribute name="Rotation" value="1 0 0 0" />
				<attribute name="Scale" value="1 1 1" />
				<attribute name="Variables" />
				<component type="StaticModel" id="16778889">
					<attribute name="Model" value="Model;Models/props/slope1x2.mdl" />
					<attribute name="Material" value="Material;Materials/props/plain.xml;Materials/props/trim.xml" />
				</component>
				<component type="RigidBody" id="16778890">
					<attribute name="Physics Position" value="-82.3345 -46.8573 -45.255" />
					<attribute name="Collision Layer" value="2" />
					<attribute name="Use Gravity" value="false" />
				</component>
				<component type="CollisionShape" id="16778891">
					<attribute name="Shape Type" value="TriangleMesh" />
					<attribute name="Model" value="Model;Models/props/slope1x2.mdl" />
				</component>
			</node>
			<node id="16777536">
				<attribute name="Is Enabled" value="true" />
				<attribute name="Name" value="platform1x2" />
				<attribute name="Tags" />
				<attribute name="Position" value="10 -15 5" />
				<attribute name="Rotation" value="0.707107 0 0.707107 0" />
				<attribute name="Scale" value="1 1 1" />
				<attribute name="Variables" />
				<component type="StaticModel" id="16778892">
					<attribute name="Model" value="Model;Models/props/platform1x2.mdl" />
					<attribute name="Material" value="Material;Materials/props/plain.xml;Materials/props/trim.xml" />
				</component>
				<component type="RigidBody" id="16778893">
					<attribute name="Physics Rotation" value="0.707107 0 0.707107 0" />
					<attribute name="Physics Position" value="-82.3345 -46.8573 -50.255" />
					<attribute name="Collision Layer" value="2" />
					<attribute name="Use Gravity" value="false" />
				</component>
				<component type="CollisionShape" id="16778894">
					<attribute name="Shape Type" value="TriangleMesh" />
					<attribute name="Model" value="Model;Models/props/platform1x2.mdl" />
				</component>
			</node>
			<node id="16777537">
				<attribute name="Is Enabled" value="true" />
				<attribute name="Name" value="slope1x2" />
				<attribute name="Tags" />
				<attribute name="Position" value="10 -5 10" />
				<attribute name="Rotation" value="1 0 0 0" />
				<attribute name="Scale" value="1 1 1" />
				<attribute name="Variables" />
				<component type="StaticModel" id="16778895">
					<attribute name="Model" value="Model;Models/props/slope1x2.mdl" />
					<attribute name="Material" value="Material;Materials/props/plain.xml;Materials/props/trim.xml" />
				</component>
				<component type="RigidBody" id="16778896">
					<attribute name="Physics Position" value="-82.3345 -36.8573 -45.255" />
					<attribute name="Collision Layer" value="2" />
					<attribute name="Use Gravity" value="false" />
				</component>
				<component type="CollisionShape" id="16778897">
					<attribute name="Shape Type" value="TriangleMesh" />
					<attribute name="Model" value="Model;Models/props/slope1x2.mdl" />
				</component>
			</node>
			<node id="16777538">
				<attribute name="Is Enabled" value="true" />
				<attribute name="Name" value="platform1x2" />
				<attribute name="Tags" />
				<attribute name="Position" value="10 -5 5" />
				<attribute name="Rotation" value="0.707107 0 0.707107 0" />
				<attribute name="Scale" value="1 1 1" />
				<attribute name="Variables" />
				<component type="StaticModel" id="16778898">
					<attribute name="Model" value="Model;Models/props/platform1x2.mdl" />
					<attribute name="Material" value="Material;Materials/props/plain.xml;Materials/props/trim.xml" />
				</component>
				<component type="RigidBody" id="16778899">
					<attribute name="Physics Rotation" value="0.707107 0 0.707107 0" />
					<attribute name="Physics Position" value="-82.3345 -36.8573 -50.255" />
					<attribute name="Collision Layer" value="2" />
					<attribute name="Use Gravity" value="false" />
				</component>
				<component type="CollisionShape" id="16778900">
					<attribute name="Shape Type" value="TriangleMesh" />
					<attribute name="Model" value="Model;Models/props/platform1x2.mdl" />
				</component>
			</node>
			<node id="16777539">
				<attribute name="Is Enabled" value="true" />
				<attribute name="Name" value="slope1x2" />
				<attribute name="Tags" />
				<attribute name="Position" value="0 -10 10" />
				<attribute name="Rotation" value="-4.37114e-008 0 1 -0" />
				<attribute name="Scale" value="1 1 1" />
				<attribute name="Variables" />
				<component type="StaticModel" id="16778901">
					<attribute name="Model" value="Model;Models/props/slope1x2.mdl" />
					<attribute name="Material" value="Material;Materials/props/plain.xml;Materials/props/trim.xml" />
				</component>
				<component type="RigidBody" id="16778902">
					<attribute name="Physics Rotation" value="-4.37114e-008 0 1 0" />
					<attribute name="Physics Position" value="-92.3345 -41.8573 -45.255" />
					<attribute name="Collision Layer" value="2" />
					<attribute name="Use Gravity" value="false" />
				</component>
				<component type="CollisionShape" id="16778903">
					<attribute name="Shape Type" value="TriangleMesh" />
					<attribute name="Model" value="Model;Models/props/slope1x2.mdl" />
				</component>
			</node>
			<node id="16777540">
				<attribute name="Is Enabled" value="true" />
				<attribute name="Name" value="platform1x2" />
				<attribute name="Tags" />
				<attribute name="Position" value="-5 -10 5" />
				<attribute name="Rotation" value="0.707107 0 0.707107 0" />
				<attribute name="Scale" value="1 1 1" />
				<attribute name="Variables" />
				<component type="StaticModel" id="16778904">
					<attribute name="Model" value="Model;Models/props/platform1x2.mdl" />
					<attribute name="Material" value="Material;Materials/props/plain.xml;Materials/props/trim.xml" />
				</component>
				<component type="RigidBody" id="16778905">
					<attribute name="Physics Rotation" value="0.707107 0 0.707107 0" />
					<attribute name="Physics Position" value="-97.3345 -41.8573 -50.255" />
					<attribute name="Collision Layer" value="2" />
					<attribute name="Use Gravity" value="false" />
				</component>
				<component type="CollisionShape" id="16778906">
					<attribute name="Shape Type" value="TriangleMesh" />
					<attribute name="Model" value="Model;Models/props/platform1x2.mdl" />
				</component>
			</node>
			<node id="16777541">
				<attribute name="Is Enabled" value="true" />
				<attribute name="Name" value="Plane" />
				<attribute name="Tags" />
				<attribute name="Position" value="0 -12 1" />
				<attribute name="Rotation" value="1 0 0 0" />
				<attribute name="Scale" value="1 1 1" />
				<attribute name="Variables" />
				<component type="StaticModel" id="16778907">
					<attribute name="Model" value="Model;Models/props/Plane.mdl" />
					<attribute name="Material" value="Material;Materials/props/trim.xml" />
				</component>
				<component type="RigidBody" id="16778908">
					<attribute name="Physics Position" value="-92.3345 -43.8573 -54.255" />
					<attribute name="Collision Layer" value="2" />
					<attribute name="Use Gravity" value="false" />
				</component>
				<component type="CollisionShape" id="16778909">
					<attribute name="Shape Type" value="TriangleMesh" />
					<attribute name="Model" value="Model;Models/props/Plane.mdl" />
				</component>
			</node>
			<node id="16777542">
				<attribute name="Is Enabled" value="true" />
				<attribute name="Name" value="Plane" />
				<attribute name="Tags" />
				<attribute name="Position" value="16.5 -14 11" />
				<attribute name="Rotation" value="1 0 0 0" />
				<attribute name="Scale" value="1 1 1" />
				<attribute name="Variables" />
				<component type="StaticModel" id="16778910">
					<attribute name="Model" value="Model;Models/props/Plane.mdl" />
					<attribute name="Material" value="Material;Materials/props/trim.xml" />
				</component>
				<component type="RigidBody" id="16778911">
					<attribute name="Physics Position" value="-75.8345 -45.8573 -44.255" />
					<attribute name="Collision Layer" value="2" />
					<attribute name="Use Gravity" value="false" />
				</component>
				<component type="CollisionShape" id="16778912">
					<attribute name="Shape Type" value="TriangleMesh" />
					<attribute name="Model" value="Model;Models/props/Plane.mdl" />
				</component>
			</node>
			<node id="16777543">
				<attribute name="Is Enabled" value="true" />
				<attribute name="Name" value="Plane" />
				<attribute name="Tags" />
				<attribute name="Position" value="0 -2 1" />
				<attribute name="Rotation" value="1 0 0 0" />
				<attribute name="Scale" value="1 1 1" />
				<attribute name="Variables" />
				<component type="StaticModel" id="16778913">
					<attribute name="Model" value="Model;Models/props/Plane.mdl" />
					<attribute name="Material" value="Material;Materials/props/trim.xml" />
				</component>
				<component type="RigidBody" id="16778914">
					<attribute name="Physics Position" value="-92.3345 -33.8573 -54.255" />
					<attribute name="Collision Layer" value="2" />
					<attribute name="Use Gravity" value="false" />
				</component>
				<component type="CollisionShape" id="16778915">
					<attribute name="Shape Type" value="TriangleMesh" />
					<attribute name="Model" value="Model;Models/props/Plane.mdl" />
				</component>
			</node>
		</node>
		<node id="16777545">
			<attribute name="Is Enabled" value="true" />
			<attribute name="Name" value="Light" />
			<attribute name="Tags" />
			<attribute name="Position" value="-1 -7 4.5" />
			<attribute name="Rotation" value="1 0 0 0" />
			<attribute name="Scale" value="1 1 1" />
			<attribute name="Variables" />
			<component type="Light" id="16778928">
				<attribute name="Color" value="1 0.6 0.14 1" />
				<attribute name="Light Shape Texture" value="TextureCube;" />
			</component>
		</node>
		<node id="16777546">
			<attribute name="Is Enabled" value="true" />
			<attribute name="Name" value="Light" />
			<attribute name="Tags" />
			<attribute name="Position" value="18 3 0" />
			<attribute name="Rotation" value="1 0 0 0" />
			<attribute name="Scale" value="1 1 1" />
			<attribute name="Variables" />
			<component type="Light" id="16778929">
				<attribute name="Color" value="1 0.6 0.14 1" />
				<attribute name="Light Shape Texture" value="TextureCube;" />
			</component>
		</node>
		<node id="16777547">
			<attribute name="Is Enabled" value="true" />
			<attribute name="Name" value="Light" />
			<attribute name="Tags" />
			<attribute name="Position" value="5.5 -1 -8.5" />
			<attribute name="Rotation" value="1 0 0 0" />
			<attribute name="Scale" value="1 1 1" />
			<attribute name="Variables" />
			<component type="Light" id="16778930">
				<attribute name="Color" value="1 0.12 0.01 1" />
				<attribute name="Brightness Multiplier" value="0.3" />
				<attribute name="Range" value="5" />
				<attribute name="Light Shape Texture" value="TextureCube;" />
			</component>
		</node>
		<node id="16777548">
			<attribute name="Is Enabled" value="true" />
			<attribute name="Name" value="Light" />
			<attribute name="Tags" />
			<attribute name="Position" value="12 -12.5 10.5" />
			<attribute name="Rotation" value="1 0 0 0" />
			<attribute name="Scale" value="1 1 1" />
			<attribute name="Variables" />
			<component type="Light" id="16778931">
				<attribute name="Color" value="1 0.6 0.14 1" />
				<attribute name="Light Shape Texture" value="TextureCube;" />
			</component>
		</node>
		<node id="16777549">
			<attribute name="Is Enabled" value="true" />
			<attribute name="Name" value="Spot" />
			<attribute name="Tags" />
			<attribute name="Position" value="-11 5 -1.5" />
			<attribute name="Rotation" value="-0.0703158 0.172588 -0.933649 0.305899" />
			<attribute name="Scale" value="1 1 1" />
			<attribute name="Variables" />
			<component type="Light" id="16778932">
				<attribute name="Light Type" value="Spot" />
				<attribute name="Color" value="0.5 0.6 1 1" />
				<attribute name="Brightness Multiplier" value="8" />
				<attribute name="Range" value="60" />
				<attribute name="Spot FOV" value="48.15" />
			</component>
		</node>
		<node id="16777550">
			<attribute name="Is Enabled" value="true" />
			<attribute name="Name" value="Light" />
			<attribute name="Tags" />
			<attribute name="Position" value="-1 -18.5 4.5" />
			<attribute name="Rotation" value="1 0 0 0" />
			<attribute name="Scale" value="1 1 1" />
			<attribute name="Variables" />
			<component type="Light" id="16778933">
				<attribute name="Color" value="1 0.6 0.14 1" />
				<attribute name="Light Shape Texture" value="TextureCube;" />
			</component>
		</node>
		<node id="16777551">
			<attribute name="Is Enabled" value="true" />
			<attribute name="Name" value="Light" />
			<attribute name="Tags" />
			<attribute name="Position" value="-8 0.5 7" />
			<attribute name="Rotation" value="1 0 0 0" />
			<attribute name="Scale" value="1 1 1" />
			<attribute name="Variables" />
			<component type="Light" id="16778934">
				<attribute name="Color" value="1 0.6 0.14 1" />
				<attribute name="Light Shape Texture" value="TextureCube;" />
			</component>
		</node>
		<node id="16777552">
			<attribute name="Is Enabled" value="true" />
			<attribute name="Name" value="Light" />
			<attribute name="Tags" />
			<attribute name="Position" value="-33.5 -1 2.5" />
			<attribute name="Rotation" value="1 0 0 0" />
			<attribute name="Scale" value="1 1 1" />
			<attribute name="Variables" />
			<component type="Light" id="16778935">
				<attribute name="Color" value="1 0.12 0.01 1" />
				<attribute name="Brightness Multiplier" value="0.3" />
				<attribute name="Range" value="5" />
				<attribute name="Light Shape Texture" value="TextureCube;" />
			</component>
		</node>
		<node id="16777553">
			<attribute name="Is Enabled" value="true" />
			<attribute name="Name" value="Light" />
			<attribute name="Tags" />
			<attribute name="Position" value="-3.5 6 5.5" />
			<attribute name="Rotation" value="1 0 0 0" />
			<attribute name="Scale" value="1 1 1" />
			<attribute name="Variables" />
			<component type="Light" id="16778936">
				<attribute name="Color" value="1 0.12 0.01 1" />
				<attribute name="Brightness Multiplier" value="0.3" />
				<attribute name="Range" value="5" />
				<attribute name="Light Shape Texture" value="TextureCube;" />
			</component>
		</node>
		<node id="16777554">
			<attribute name="Is Enabled" value="true" />
			<attribute name="Name" value="Light" />
			<attribute name="Tags" />
			<attribute name="Position" value="-33.5 -1 -6" />
			<attribute name="Rotation" value="1 0 0 0" />
			<attribute name="Scale" value="1 1 1" />
			<attribute name="Variables" />
			<component type="Light" id="16778937">
				<attribute name="Color" value="1 0.12 0.01 1" />
				<attribute name="Brightness Multiplier" value="0.3" />
				<attribute name="Range" value="5" />
				<attribute name="Light Shape Texture" value="TextureCube;" />
			</component>
		</node>
		<node id="16777555">
			<attribute name="Is Enabled" value="true" />
			<attribute name="Name" value="Light" />
			<attribute name="Tags" />
			<attribute name="Position" value="-3.5 -1 -8.5" />
			<attribute name="Rotation" value="1 0 0 0" />
			<attribute name="Scale" value="1 1 1" />
			<attribute name="Variables" />
			<component type="Light" id="16778938">
				<attribute name="Color" value="1 0.12 0.01 1" />
				<attribute name="Brightness Multiplier" value="0.3" />
				<attribute name="Range" value="5" />
				<attribute name="Light Shape Texture" value="TextureCube;" />
			</component>
		</node>
		<node id="16777556">
			<attribute name="Is Enabled" value="true" />
			<attribute name="Name" value="Light" />
			<attribute name="Tags" />
			<attribute name="Position" value="14 -3 6" />
			<attribute name="Rotation" value="1 0 0 0" />
			<attribute name="Scale" value="1 1 1" />
			<attribute name="Variables" />
			<component type="Light" id="16778939">
				<attribute name="Color" value="1 0.6 0.14 1" />
				<attribute name="Light Shape Texture" value="TextureCube;" />
			</component>
		</node>
		<node id="16777557">
			<attribute name="Is Enabled" value="true" />
			<attribute name="Name" value="Spot" />
			<attribute name="Tags" />
			<attribute name="Position" value="15 -1 16.5" />
			<attribute name="Rotation" value="0.893159 0.221713 -0.37833 -0.0998855" />
			<attribute name="Scale" value="1 1 1" />
			<attribute name="Variables" />
			<component type="Light" id="16778940">
				<attribute name="Light Type" value="Spot" />
				<attribute name="Color" value="0.5 0.6 1 1" />
				<attribute name="Brightness Multiplier" value="8" />
				<attribute name="Range" value="60" />
				<attribute name="Spot FOV" value="48.15" />
			</component>
		</node>
		<node id="16777558">
			<attribute name="Is Enabled" value="true" />
			<attribute name="Name" value="Spot" />
			<attribute name="Tags" />
			<attribute name="Position" value="22 2.5 -1" />
			<attribute name="Rotation" value="0.402911 0.230946 -0.882345 0.0761444" />
			<attribute name="Scale" value="1 1 1" />
			<attribute name="Variables" />
			<component type="Light" id="16778941">
				<attribute name="Light Type" value="Spot" />
				<attribute name="Color" value="0.5 0.6 1 1" />
				<attribute name="Brightness Multiplier" value="8" />
				<attribute name="Range" value="60" />
				<attribute name="Spot FOV" value="48.15" />
			</component>
		</node>
	</node>
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
		<attribute name="mapFile" value="XMLFile;Scenes/MZGN_ifsland.xml" />
		<attribute name="radius" value="1.61623" />
		<attribute name="maxRadius" value="3.23246" />
		<attribute name="defs" value="FCT_MZGN" />
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
