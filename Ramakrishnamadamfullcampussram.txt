import java.util.ArrayList;

// ============================================================
// SRI RAMAKRISHNA MATH 3D CAMPUS SKETCH (COMPLETE)
// 1. 3-Storey Swami Vivekananda Library & Extended Campus Wing (Right)
// 2. Grand Sloped-Roof Gopuram / Archway Entrance Gate on Left Side
//    (with baluster parapet, central circular Ramakrishna Emblem crest,
//    paired terracotta-rose piers, notice board, and festive light bulbs)
// 3. Complete Perimeter Compound Wall enclosing the entire campus
//    (left, right, front, and rear walls with cream pilasters, spherical
//    globe finials, and wrought-iron security grilles)
// 4. Sri Ramakrishna Math Universal Temple positioned behind the
//    courtyard plaza, visible directly through the new side entrance arch
//
// Coordinate convention:
//   X = left entrance arch & temple (-) / 3-storey library & stair tower (+)
//   Y = vertical (courtyard floor = 0, upward is negative)
//   Z = deep rear campus (-1060) to front street (+360)
// ============================================================

// ---------------- VIBRANT HIGH-CONTRAST PALETTE ----------

final int CREAM_BRIGHT = 0xFFFFF4DC; // Sunlit warm cream walls
final int CREAM_GOLD   = 0xFFF3D59B; // Rich golden-ochre frieze & stringcourses
final int CREAM_SHADE  = 0xFFD9B87E; // Warm shadow trim & cornice undercuts
final int IVORY_TRIM   = 0xFFFFFCF2; // Crisp high-contrast arch & window frames

final int ROSE_VIBRANT = 0xFFE38388; // Vibrant terracotta-rose columns & slabs
final int ROSE_DEEP    = 0xFFB54E56; // Rich crimson-terracotta plinths & reveals
final int ROSE_SOFT    = 0xFFF4B3B6; // Warm rose soffits & stair risers

// Universal Temple & Entrance Arch Terracotta-Rose & Cream Palette
final int PINK_MAIN    = 0xFFD48887;
final int PINK_LIGHT   = 0xFFE8A7A5;
final int PINK_DARK    = 0xFFA65B5B;
final int PINK_DEEP    = 0xFF823F40;
final int CREAM_TEMPLE = 0xFFF8E9D6;
final int MARBLE_WHITE = 0xFFF4F1EB;
final int MARBLE_GREY  = 0xFF9699A1;
final int EMBLEM_BLUE  = 0xFF2F88CC;
final int EMBLEM_SUN   = 0xFFFFBE33;
final int BLACK_PLAQUE = 0xFF1F2226;

final int PEACH_SLAB   = 0xFFF7C996; // Warm ochre-peach exterior stair flights
final int WHITE        = 0xFFFFFFFF;
final int STEEL_BRIGHT = 0xFFDCE6EE; // Bright brushed stainless-steel railings
final int STEEL_DARK   = 0xFF3E4A56; // High-contrast dark charcoal ironwork
final int BLUE_RAIL    = 0xFF1E56B3; // Cobalt blue street guardrail

final int SIGN_YELLOW  = 0xFFFFE63B; // Vivid marigold-yellow Tamil banner
final int SIGN_WHITE   = 0xFFFFFFFF; // Pure white English title band
final int SIGN_RED     = 0xFFD92B2B; // Vivid vermilion red signboard panels
final int SIGN_BLUE    = 0xFF104A8E; // Deep royal blue lettering

final int GLASS_DAY    = 0xFF1E3B4D; // Deep teal-slate reflective window glass
final int GLASS_LIT    = 0xFFFFDF8E; // Warm golden interior illumination
final int WOOD_TEAK    = 0xFF5A2A14; // Rich teakwood window frames & shutters

final int PAVER_CREAM  = 0xFFEBD9BE; // Courtyard cream interlocking pavers
final int PAVER_RED    = 0xFFC45245; // Terracotta-red courtyard paver bands
final int ROAD_ASPHALT = 0xFF333942; // Crisp dark street asphalt

final int LEAF_DEEP    = 0xFF12471D; // Deep emerald canopy shadow
final int LEAF_VIVID   = 0xFF288234; // Vibrant tropical green foliage
final int LEAF_LIME    = 0xFF58B847; // Sun-kissed lime leaf highlights
final int POT_GREEN    = 0xFF199E3D; // Vivid green planter rows
final int TRUNK_BROWN  = 0xFF5C4332;

// ---------------- CAMERA & UI STATE ---------------------

float cameraYaw   = -0.22;
float cameraPitch =  0.12;
float cameraZoom  =  0.72;

float targetYaw   = -0.22;
float targetPitch =  0.12;
float targetZoom  =  0.72;

boolean nightMode = false; // Vibrant sunlit contrast by default
boolean showHUD   = true;

float previousMouseX;
float previousMouseY;
int frameNumber = 0;

// ============================================================
// SETUP
// ============================================================

void setup() {
  fullScreen(P3D);
  frameRate(30);
  smooth(4);
}

// ============================================================
// MAIN DRAW LOOP
// ============================================================

void draw() {
  cameraYaw   = lerp(cameraYaw, targetYaw, 0.14);
  cameraPitch = lerp(cameraPitch, targetPitch, 0.14);
  cameraZoom  = lerp(cameraZoom, targetZoom, 0.16);

  if (nightMode) {
    background(10, 16, 28);
  } else {
    background(92, 168, 238);
  }

  pushMatrix();

  translate(width * 0.56, height * 0.68, 0);
  float fit = min(width / 1160.0, height / 940.0);
  scale(fit * cameraZoom);

  rotateX(cameraPitch);
  rotateY(cameraYaw);

  if (!nightMode) {
    noLights();
    drawSkyClouds();
  }

  // High-Contrast Three-Point Architectural Lighting
  lights();
  if (nightMode) {
    ambientLight(85, 82, 95);
    directionalLight(210, 195, 175, 0.30, 0.50, -0.80);
    pointLight(255, 215, 145, 0, -95, 45);
    pointLight(255, 215, 145, 0, -235, 45);
    pointLight(255, 215, 145, 0, -365, 45);
    pointLight(255, 205, 125, -460, -220, -640);
    pointLight(255, 205, 130, -460, -140, 220);
  } else {
    ambientLight(138, 134, 130);
    directionalLight(255, 246, 228, -0.48, 0.72, -0.50);
    directionalLight(105, 140, 185, 0.55, 0.30, 0.65);
  }

  drawPatternedCourtyardAndStreet();
  drawThreeStoreyLibraryBuilding();
  drawFrontPorticoAndCuspedArches();
  drawCantileverBalconiesAndThreeStoreyStairs();
  drawExtendedRoofParapetAndSign();
  drawFullPerimeterCompoundWallAndSideEntranceArch();
  drawLushCampusTreesAndPlanters();

  // Universal Temple positioned behind the side entrance arch & courtyard
  drawUniversalTempleBehind();

  popMatrix();

  if (showHUD) {
    drawHUD();
  } else {
    drawMiniHudButton();
  }

  frameNumber++;
}

// ============================================================
// 3D GEOMETRY & TEXTURE HELPERS
// ============================================================

void boxAt(float x, float y, float z, float w, float h, float d, int c) {
  pushMatrix();
  translate(x, y, z);
  noStroke();
  fill(c);
  box(w, h, d);
  popMatrix();
}

void texturedWallAt(float x, float y, float z, float w, float h, float d, int baseCol, int grooveCol) {
  boxAt(x, y, z, w, h, d, baseCol);
  int courses = max(2, int(h / 24));
  for (int i = 1; i < courses; i++) {
    float gy = y - h * 0.5 + i * (h / (float)courses);
    boxAt(x, gy, z, w + 1.2, 1.6, d + 1.2, grooveCol);
  }
}

void cylinderY(float x, float yCenter, float z, float r, float h, int segs, int c) {
  cylinderYTapered(x, yCenter, z, r, r, h, segs, c);
}

void cylinderYTapered(float x, float yCenter, float z, float rBot, float rTop, float h, int segs, int c) {
  pushMatrix();
  translate(x, yCenter, z);
  noStroke();
  fill(c);
  float y0 = -h * 0.5;
  float y1 =  h * 0.5;

  beginShape(QUAD_STRIP);
  for (int i = 0; i <= segs; i++) {
    float a = i * TWO_PI / segs;
    float ca = cos(a);
    float sa = sin(a);
    normal(ca, 0, sa);
    vertex(ca * rTop, y0, sa * rTop);
    vertex(ca * rBot, y1, sa * rBot);
  }
  endShape();

  beginShape(TRIANGLE_FAN);
  normal(0, -1, 0);
  vertex(0, y0, 0);
  for (int i = 0; i <= segs; i++) {
    float a = i * TWO_PI / segs;
    vertex(cos(a) * rTop, y0, sin(a) * rTop);
  }
  endShape();

  beginShape(TRIANGLE_FAN);
  normal(0, 1, 0);
  vertex(0, y1, 0);
  for (int i = 0; i <= segs; i++) {
    float a = -i * TWO_PI / segs;
    vertex(cos(a) * rBot, y1, sin(a) * rBot);
  }
  endShape();
  popMatrix();
}

void drawCuspedArch(float cx, float ySpring, float z, float spanW, float archH, float depth, int wallCol, int trimCol) {
  float halfW = spanW * 0.5;
  float yTop = ySpring - archH - 14;
  int segs = 36;

  pushMatrix();
  translate(cx, 0, z);
  noStroke();

  fill(wallCol);
  beginShape(QUAD_STRIP);
  normal(0, 0, 1);
  for (int i = 0; i <= segs; i++) {
    float t = i / (float)segs;
    float a = PI + t * PI;
    float lobe = abs(sin(t * PI * 5.0)) * 6.8;
    float px = cos(a) * halfW;
    float py = ySpring + sin(a) * (archH - lobe);
    vertex(px, yTop, depth * 0.5);
    vertex(px, py,   depth * 0.5);
  }
  endShape();

  beginShape(QUAD_STRIP);
  normal(0, 0, -1);
  for (int i = 0; i <= segs; i++) {
    float t = i / (float)segs;
    float a = PI + t * PI;
    float lobe = abs(sin(t * PI * 5.0)) * 6.8;
    float px = cos(a) * halfW;
    float py = ySpring + sin(a) * (archH - lobe);
    vertex(px, py,   -depth * 0.5);
    vertex(px, yTop, -depth * 0.5);
  }
  endShape();

  fill(trimCol);
  for (int i = 0; i < segs; i++) {
    float t0 = i / (float)segs;
    float t1 = (i + 1) / (float)segs;
    float a0 = PI + t0 * PI;
    float a1 = PI + t1 * PI;
    float lobe0 = abs(sin(t0 * PI * 5.0)) * 6.8;
    float lobe1 = abs(sin(t1 * PI * 5.0)) * 6.8;

    float x0 = cos(a0) * halfW;
    float y0 = ySpring + sin(a0) * (archH - lobe0);
    float x1 = cos(a1) * halfW;
    float y1 = ySpring + sin(a1) * (archH - lobe1);

    float xm = (x0 + x1) * 0.5;
    float ym = (y0 + y1) * 0.5;
    float segLen = dist(x0, y0, x1, y1) + 1.6;
    float ang = atan2(y1 - y0, x1 - x0);

    pushMatrix();
    translate(xm, ym, 0);
    rotateZ(ang);
    box(segLen, 6.0, depth + 4.5);
    popMatrix();
  }

  popMatrix();
}

void drawSkyClouds() {
  fill(255, 255, 255, 195);
  noStroke();
  sphereDetail(10);

  float[][] clouds = {
    {-580, -560, -920, 115},
    {-420, -590, -950, 140},
    {-250, -550, -880, 100},
    { 240, -540, -820, 120},
    { 360, -575, -800, 100},
    { -80, -620, -1000, 150}
  };
  for (int i = 0; i < clouds.length; i++) {
    pushMatrix();
    translate(clouds[i][0], clouds[i][1], clouds[i][2]);
    scale(1.5, 0.75, 0.8);
    sphere(clouds[i][3]);
    popMatrix();
  }
}

// ============================================================
// PATTERNED INTERLOCKING PAVER COURTYARD & STREET
// ============================================================

void drawPatternedCourtyardAndStreet() {
  // Base surrounding ground outside compound walls
  boxAt(-210, 12, -390, 2100, 20, 1850, 0xFF5C5248);

  // Enclosed Campus Courtyard Paver Plaza inside perimeter walls (x = -790..350, z = -1040..240)
  boxAt(-220, -1, -400, 1140, 4, 1280, PAVER_CREAM);

  // Interlocking Red Terracotta Paver Bands across the Courtyard
  for (float zBand = -980; zBand <= 210; zBand += 68) {
    boxAt(-220, -2.2, zBand, 1130, 1.5, 12, PAVER_RED);
  }
  for (float xBand = -750; xBand <= 310; xBand += 96) {
    boxAt(xBand, -2.1, -400, 10, 1.5, 1270, PAVER_RED);
  }

  // Raised 3-Storey Library Building Plinth extending deep behind (z = -545..76, y = -12..0)
  boxAt(0, -6, -235, 422, 12, 622, ROSE_DEEP);
  boxAt(0, -12, -235, 426, 3, 626, IVORY_TRIM);

  // Sidewalk & Asphalt Street in front of compound wall (z = 248..450)
  boxAt(-220, 2, 272, 2100, 6, 46, 0xFF9E988E);
  boxAt(-220, 5, 372, 2100, 6, 160, ROAD_ASPHALT);

  // Vivid Blue Tubular Pedestrian Guardrail along front sidewalk
  stroke(BLUE_RAIL);
  strokeWeight(5.0);
  line(-335, -42, 310, -165, -42, 310);
  line(-335, -22, 310, -165, -22, 310);
  for (float rx = -330; rx <= -170; rx += 38) {
    line(rx, 2, 310, rx, -44, 310);
  }
  noStroke();
}

// ============================================================
// 3-STOREY LIBRARY BUILDING (GROUND + 1ST + 2ND FLOOR, DEEP WING)
// ============================================================

void drawThreeStoreyLibraryBuilding() {
  // Deep Rear Wall at z = -540 (full 3-storey height = 400)
  texturedWallAt(0, -200, -540, 396, 400, 14, CREAM_BRIGHT, CREAM_SHADE);

  // Long Left (-X) & Right (+X) Exterior Walls extending from z = +16 back to z = -540
  texturedWallAt(-190, -200, -262, 14, 400, 556, CREAM_BRIGHT, CREAM_SHADE);
  texturedWallAt( 190, -200, -262, 14, 400, 556, CREAM_BRIGHT, CREAM_SHADE);

  // Front Recessed Walls for all 3 storeys (z = 14)
  texturedWallAt(0,  -76, 14, 376, 128, 10, CREAM_BRIGHT, CREAM_SHADE);
  texturedWallAt(0, -205, 14, 376, 130, 10, CREAM_BRIGHT, CREAM_SHADE);
  texturedWallAt(0, -335, 14, 376, 130, 10, CREAM_BRIGHT, CREAM_SHADE);

  // Three Continuous Horizontal Floor Slabs & Golden Cornice Bands (z = +78 to -544)
  float[] slabY = {-140, -270, -400};
  for (int s = 0; s < slabY.length; s++) {
    float sy = slabY[s];
    boxAt(0, sy,     -234, 408, 12, 624, ROSE_SOFT);
    boxAt(0, sy + 6, -234, 414, 5,  630, CREAM_GOLD);
    boxAt(0, sy - 6, -234, 412, 4,  628, IVORY_TRIM);
  }

  // Side Wing 3-Storey Colonnade & Two Upper Tiers of Windows along BOTH Long Flanks
  int winFill = nightMode ? GLASS_LIT : GLASS_DAY;
  float[] bayZ = {-40, -110, -180, -250, -320, -390, -460, -520};

  for (int side = -1; side <= 1; side += 2) {
    float xWall = side * 197;
    float xCol  = side * 204;

    boxAt(side * 200, -140, -270, 22, 10, 540, CREAM_GOLD);

    for (int i = 0; i < bayZ.length; i++) {
      float wz = bayZ[i];

      // Ground-floor square veranda pier
      boxAt(xCol, -74, wz, 14, 124, 14, CREAM_BRIGHT);
      boxAt(xCol, -18, wz, 18, 12,  18, ROSE_VIBRANT);
      boxAt(xCol, -130, wz, 18, 10, 18, CREAM_GOLD);

      // Ground-floor window/doorway on inner wall
      boxAt(xWall, -68, wz - 32, 4, 58, 36, WOOD_TEAK);
      boxAt(xWall + side * 1, -68, wz - 32, 3, 50, 28, winFill);

      // 1st-floor & 2nd-floor rhythmic pilasters along side facade
      for (int flr = 1; flr <= 2; flr++) {
        float yCenter = (flr == 1) ? -205 : -335;
        float yBaseP  = (flr == 1) ? -150 : -280;
        float yTopP   = (flr == 1) ? -260 : -390;

        boxAt(xWall, yCenter, wz, 10, 118, 14, IVORY_TRIM);
        boxAt(xWall, yBaseP,  wz, 14, 10,  18, ROSE_VIBRANT);
        boxAt(xWall, yTopP,   wz, 14, 10,  18, CREAM_GOLD);

        if (i < bayZ.length - 1) {
          float midZ = wz - 35;
          boxAt(xWall, yCenter + 2, midZ, 5, 56, 38, IVORY_TRIM);
          boxAt(xWall + side * 1, yCenter + 2, midZ, 4, 48, 30, WOOD_TEAK);
          boxAt(xWall + side * 2, yCenter + 2, midZ, 3, 42, 24, winFill);
          boxAt(xWall + side * 2.5, yCenter + 2, midZ, 2, 42, 2.5, WOOD_TEAK);
          boxAt(xWall + side * 2.5, yCenter + 2, midZ, 2, 2.5, 24, WOOD_TEAK);
          boxAt(xWall + side * 3, yCenter - 32, midZ, 8, 6, 44, CREAM_GOLD);
          boxAt(xWall + side * 3, yBaseP + 2,   midZ, 6, 20, 42, IVORY_TRIM);
        }
      }
    }
  }

  // Front Portico Interior Doors & Windows across all 3 storeys (z = 19)
  float[] floorDoorY = {-56, -192, -322};
  float[] floorWinY  = {-62, -198, -328};
  for (int flr = 0; flr < 3; flr++) {
    float dy = floorDoorY[flr];
    float wy = floorWinY[flr];
    boxAt(   0, dy, 19, 56, 86, 4, WOOD_TEAK);
    boxAt(   0, dy, 20, 46, 76, 3, winFill);
    boxAt(-115, wy, 19, 40, 56, 4, WOOD_TEAK);
    boxAt(-115, wy, 20, 32, 48, 3, winFill);
    boxAt( 115, wy, 19, 40, 56, 4, WOOD_TEAK);
    boxAt( 115, wy, 20, 32, 48, 3, winFill);
  }
}

// ============================================================
// FRONT PORTICO COLUMNS & VIBRANT CUSPED ARCHES (ALL 3 STOREYS)
// ============================================================

void drawFrontPorticoAndCuspedArches() {
  float zCol = 66;
  float[] pierX = {-172, -56, 56, 172};
  float[] levelBot = {-12,  -146, -276};
  float[] levelTop = {-134, -264, -394};

  for (int level = 0; level < 3; level++) {
    float yBot = levelBot[level];
    float yTop = levelTop[level];
    float colH = abs(yTop - yBot);
    float yMid = (yBot + yTop) * 0.5;

    for (int i = 0; i < pierX.length; i++) {
      float px = pierX[i];

      if (i == 1 || i == 2) {
        boxAt(px - 8, yMid, zCol, 13, colH, 15, ROSE_VIBRANT);
        cylinderY(px + 8, yMid, zCol, 6.8, colH, 16, ROSE_VIBRANT);
        boxAt(px - 8, yMid, zCol, 15, 4, 17, IVORY_TRIM);
        cylinderY(px + 8, yMid, zCol, 7.6, 4, 16, IVORY_TRIM);
        boxAt(px, yTop + 9, zCol, 36, 10, 24, ROSE_VIBRANT);
        boxAt(px, yTop + 3, zCol, 40, 6,  28, IVORY_TRIM);
        boxAt(px, yBot - 5, zCol, 34, 10, 22, ROSE_DEEP);
      } else {
        boxAt(px, yMid, zCol, 20, colH, 20, ROSE_VIBRANT);
        boxAt(px, yMid, zCol, 22, 5,  22, IVORY_TRIM);
        boxAt(px, yTop + 9, zCol, 28, 10, 28, ROSE_VIBRANT);
        boxAt(px, yTop + 3, zCol, 32, 6,  32, IVORY_TRIM);
        boxAt(px, yBot - 5, zCol, 26, 10, 26, ROSE_DEEP);
      }
    }

    float ySpring = yTop + 34;
    drawCuspedArch(-114, ySpring, zCol, 96, 22, 10, ROSE_SOFT, IVORY_TRIM);
    drawCuspedArch(   0, ySpring, zCol, 92, 24, 10, ROSE_SOFT, IVORY_TRIM);
    drawCuspedArch( 114, ySpring, zCol, 96, 22, 10, ROSE_SOFT, IVORY_TRIM);
  }
}

// ============================================================
// CANTILEVER BALCONIES, RAILINGS & 3-STOREY STAIRCASE TOWER
// ============================================================

void drawCantileverBalconiesAndThreeStoreyStairs() {
  float[] deckY = {-142, -272};
  for (int d = 0; d < deckY.length; d++) {
    float ySlab = deckY[d];
    float yRail = ySlab - 6;
    boxAt(0, ySlab, 92, 154, 11, 52, ROSE_VIBRANT);
    boxAt(0, yRail, 92, 158, 4,  56, IVORY_TRIM);
    drawSteelRailing(-75, 116, 75, 116, yRail, 34, 18);
    drawSteelRailing(-75,  76, -75, 116, yRail, 34, 6);
    drawSteelRailing( 75,  76,  75, 116, yRail, 34, 6);
  }

  float[] cornerY = {-74, -142, -272};
  for (int side = -1; side <= 1; side += 2) {
    float bx = side * 165;
    for (int lvl = 0; lvl < cornerY.length; lvl++) {
      float cy = cornerY[lvl];
      float ry = cy - 5;
      boxAt(bx, cy, 74, 78, 10, 70, ROSE_VIBRANT);
      boxAt(bx, ry, 74, 82, 4,  74, IVORY_TRIM);
      drawSteelRailing(bx - 37, 108, bx + 37, 108, ry, 33, 9);
      drawSteelRailing(bx - 37,  42, bx - 37, 108, ry, 33, 8);
      drawSteelRailing(bx + 37,  42, bx + 37, 108, ry, 33, 8);
    }
  }

  drawLibrarySignboard(0, -158, 119, 112, 30);

  for (int side = -1; side <= 1; side += 2) {
    float sx = side * 124;

    for (int i = 0; i < 10; i++) {
      float t = i / 9.0;
      float sy = lerp(-12, -74, t);
      float sz = lerp(112, 54, t);
      boxAt(sx, sy, sz, 34, 8, 9, PEACH_SLAB);
      boxAt(sx - side * 16, sy - 16, sz, 2.5, 30, 3, STEEL_BRIGHT);
      boxAt(sx - side * 16, sy - 30, sz, 3.8, 3, 10, STEEL_BRIGHT);
    }

    for (int i = 0; i < 10; i++) {
      float t = i / 9.0;
      float sy = lerp(-74, -142, t);
      float sz = lerp(54, 106, t);
      boxAt(sx, sy, sz, 34, 8, 9, PEACH_SLAB);
      boxAt(sx - side * 16, sy - 16, sz, 2.5, 30, 3, STEEL_BRIGHT);
      boxAt(sx - side * 16, sy - 30, sz, 3.8, 3, 10, STEEL_BRIGHT);
    }

    for (int i = 0; i < 14; i++) {
      float t = i / 13.0;
      float sy = lerp(-142, -272, t);
      float sz = lerp(106, 54, t);
      boxAt(sx, sy, sz, 34, 8, 9, PEACH_SLAB);
      boxAt(sx - side * 16, sy - 16, sz, 2.5, 30, 3, STEEL_BRIGHT);
      boxAt(sx - side * 16, sy - 30, sz, 3.8, 3, 10, STEEL_BRIGHT);
    }
  }

  drawRightWingThreeStoreyStairTower();
}

void drawRightWingThreeStoreyStairTower() {
  float tx = 228;
  boxAt(246, -135, -10,  12, 270, 12, CREAM_BRIGHT);
  boxAt(246, -135, -115, 12, 270, 12, CREAM_BRIGHT);

  boxAt(tx, -74, -5, 54, 10, 62, PEACH_SLAB);
  drawSteelRailing(tx + 25, -34, tx + 25, 24, -79, 32, 8);
  drawSteelRailing(tx - 24,  24, tx + 25, 24, -79, 32, 6);

  boxAt(tx, -142, -105, 54, 10, 62, PEACH_SLAB);
  drawSteelRailing(tx + 25, -134, tx + 25, -76, -147, 32, 8);

  boxAt(tx, -206, -5, 54, 10, 62, PEACH_SLAB);
  drawSteelRailing(tx + 25, -34, tx + 25, 24, -211, 32, 8);
  drawSteelRailing(tx - 24,  24, tx + 25, 24, -211, 32, 6);

  boxAt(tx, -272, -105, 54, 10, 62, PEACH_SLAB);
  drawSteelRailing(tx + 25, -134, tx + 25, -76, -277, 32, 8);
  drawSteelRailing(tx - 24, -134, tx + 25, -134, -277, 32, 6);

  for (int i = 0; i < 11; i++) {
    float t = i / 10.0;
    float sy = lerp(-6, -74, t);
    float sz = lerp(42, 22, t);
    boxAt(tx, sy, sz, 44, 7, 8, PEACH_SLAB);
    boxAt(tx + 21, sy - 16, sz, 2.5, 30, 3, STEEL_BRIGHT);
    boxAt(tx + 21, sy - 30, sz, 3.5, 3, 8, STEEL_BRIGHT);
  }

  for (int i = 0; i < 11; i++) {
    float t = i / 10.0;
    float sy = lerp(-74, -142, t);
    float sz = lerp(-32, -82, t);
    boxAt(tx, sy, sz, 44, 7, 8, PEACH_SLAB);
    boxAt(tx + 21, sy - 16, sz, 2.5, 30, 3, STEEL_BRIGHT);
    boxAt(tx + 21, sy - 30, sz, 3.5, 3, 8, STEEL_BRIGHT);
  }

  for (int i = 0; i < 11; i++) {
    float t = i / 10.0;
    float sy = lerp(-142, -206, t);
    float sz = lerp(-78, -30, t);
    boxAt(tx, sy, sz, 44, 7, 8, PEACH_SLAB);
    boxAt(tx + 21, sy - 16, sz, 2.5, 30, 3, STEEL_BRIGHT);
    boxAt(tx + 21, sy - 30, sz, 3.5, 3, 8, STEEL_BRIGHT);
  }

  for (int i = 0; i < 11; i++) {
    float t = i / 10.0;
    float sy = lerp(-206, -272, t);
    float sz = lerp(-32, -82, t);
    boxAt(tx, sy, sz, 44, 7, 8, PEACH_SLAB);
    boxAt(tx + 21, sy - 16, sz, 2.5, 30, 3, STEEL_BRIGHT);
    boxAt(tx + 21, sy - 30, sz, 3.5, 3, 8, STEEL_BRIGHT);
  }
}

void drawSteelRailing(float x1, float z1, float x2, float z2, float yBase, float h, int bars) {
  float dx = x2 - x1;
  float dz = z2 - z1;
  float len = sqrt(dx * dx + dz * dz);
  float ang = -atan2(dz, dx);
  float cx = (x1 + x2) * 0.5;
  float cz = (z1 + z2) * 0.5;

  pushMatrix();
  translate(cx, yBase, cz);
  rotateY(ang);

  boxAt(0, -h, 0, len, 2.8, 2.8, STEEL_BRIGHT);
  boxAt(0, -5, 0, len, 2.2, 2.2, STEEL_BRIGHT);

  boxAt(-len * 0.5, -h * 0.5, 0, 3.5, h, 3.5, STEEL_DARK);
  boxAt( len * 0.5, -h * 0.5, 0, 3.5, h, 3.5, STEEL_DARK);

  for (int i = 1; i < bars; i++) {
    float bx = lerp(-len * 0.5, len * 0.5, i / (float)bars);
    boxAt(bx, -h * 0.5, 0, 1.6, h, 1.6, STEEL_BRIGHT);
  }

  popMatrix();
}

// ============================================================
// 3-STOREY ROOF PARAPET & SIGNBOARDS
// ============================================================

void drawExtendedRoofParapetAndSign() {
  boxAt(0, -415, 76, 410, 30, 10, CREAM_GOLD);
  boxAt(0, -431, 76, 418, 6,  15, ROSE_VIBRANT);
  boxAt(0, -426, 78, 414, 4,  13, IVORY_TRIM);

  boxAt(-199, -415, -234, 10, 30, 626, CREAM_GOLD);
  boxAt(-199, -431, -234, 15, 6,  632, ROSE_VIBRANT);
  boxAt(-201, -426, -234, 13, 4,  630, IVORY_TRIM);

  boxAt( 199, -415, -234, 10, 30, 626, CREAM_GOLD);
  boxAt( 199, -431, -234, 15, 6,  632, ROSE_VIBRANT);
  boxAt( 201, -426, -234, 13, 4,  630, IVORY_TRIM);

  boxAt(   0, -415, -542, 410, 30, 10, CREAM_GOLD);
  boxAt(   0, -431, -542, 418, 6,  15, ROSE_VIBRANT);

  boxAt(0, -441, 78, 228, 22, 12, CREAM_BRIGHT);
  boxAt(0, -453, 78, 236, 6,  16, ROSE_VIBRANT);
  boxAt(0, -449, 80, 232, 4,  14, IVORY_TRIM);

  boxAt(-199, -439, -140, 12, 18, 180, CREAM_BRIGHT);
  boxAt(-199, -449, -140, 16, 5,  186, ROSE_VIBRANT);

  boxAt(0, -440, 85, 138, 14, 4, IVORY_TRIM);
  boxAt(0, -440, 87, 130, 10, 2, ROSE_DEEP);
}

void drawLibrarySignboard(float x, float y, float z, float w, float h) {
  pushMatrix();
  translate(x, y, z);

  boxAt(0, 0, 0, w + 4, h + 4, 4, IVORY_TRIM);
  boxAt(0, -h * 0.30, 2.2, w, h * 0.36, 1.5, SIGN_YELLOW);
  boxAt(0,  h * 0.04, 2.2, w, h * 0.32, 1.5, SIGN_WHITE);
  boxAt(0,  h * 0.34, 2.2, w, h * 0.28, 1.5, SIGN_WHITE);
  boxAt(-w * 0.38, h * 0.34, 3.0, w * 0.22, h * 0.24, 1.0, SIGN_RED);
  boxAt( w * 0.38, h * 0.34, 3.0, w * 0.22, h * 0.24, 1.0, SIGN_RED);

  pushMatrix();
  translate(-w * 0.40, -h * 0.08, 3.5);
  noStroke();
  fill(0xFF196B33);
  ellipse(0, 0, h * 0.48, h * 0.48);
  fill(0xFF2898E8);
  ellipse(0, 0, h * 0.32, h * 0.32);
  fill(0xFFFFD026);
  arc(0, 1, h * 0.22, h * 0.22, PI, TWO_PI);
  popMatrix();

  noStroke();
  fill(SIGN_RED);
  for (float tx = -w * 0.24; tx <= w * 0.42; tx += 9) {
    boxAt(tx, -h * 0.30, 3.2, 6.5, h * 0.14, 0.8, SIGN_RED);
  }
  fill(SIGN_BLUE);
  for (float ex = -w * 0.24; ex <= w * 0.44; ex += 7.5) {
    boxAt(ex,  h * 0.04, 3.2, 5.5, h * 0.15, 0.8, SIGN_BLUE);
  }

  popMatrix();
}

// ============================================================
// COMPLETE PERIMETER COMPOUND WALL & SIDE ENTRANCE ARCH GATEWAY
// Encloses entire campus:
//   - Front wall along z = +240 (from x = -790 to x = +350)
//   - Grand Sloped-Roof Gopuram Entrance Archway on left front (x = -460, z = +240)
//     aligned directly in front of the Universal Temple!
//   - Left perimeter wall along x = -790 (z = +240 back to -1040)
//   - Right perimeter wall along x = +350 (z = +240 back to -1040)
//   - Rear perimeter wall along z = -1040 (x = -790 to +350)
// ============================================================

void drawFullPerimeterCompoundWallAndSideEntranceArch() {
  float zFront = 240;
  float zRear  = -1040;
  float xLeft  = -790;
  float xRight = 350;

  // 1. FRONT WALL SEGMENT A: Right of Library Gate (x = +130 to +350, z = +240)
  drawCompoundWallRunX(130, 350, zFront);

  // 2. FRONT LIBRARY GATE (x = -124 to +124, z = +240)
  drawLibraryMainGate(0, zFront);

  // 3. FRONT WALL SEGMENT B: Between Library Gate (-130) & Side Entrance Arch (-310)
  drawCompoundWallRunX(-310, -130, zFront);

  // 4. GRAND SIDE ENTRANCE ARCHWAY (x = -460, z = +240, span = 300 units wide)
  //    Aligned with the courtyard avenue & Universal Temple behind!
  drawGrandSideEntranceArchway(-460, zFront);

  // 5. FRONT WALL SEGMENT C: Left of Side Entrance Arch (x = -790 to -610, z = +240)
  drawCompoundWallRunX(-790, -610, zFront);

  // 6. LEFT PERIMETER COMPOUND WALL (x = -790, z = +240 back to -1040)
  drawCompoundWallRunZ(xLeft, zRear, zFront);

  // 7. RIGHT PERIMETER COMPOUND WALL (x = +350, z = +240 back to -1040)
  drawCompoundWallRunZ(xRight, zRear, zFront);

  // 8. REAR PERIMETER COMPOUND WALL (z = -1040, x = -790 to +350)
  drawCompoundWallRunX(xLeft, xRight, zRear);

  // 9. FOUR CORNER COMPOUND BASTION PILLARS WITH SPHERICAL FINIALS
  float[][] wallCorners = {
    {xLeft,  zFront},
    {xRight, zFront},
    {xLeft,  zRear},
    {xRight, zRear}
  };
  for (int c = 0; c < wallCorners.length; c++) {
    drawCompoundPierWithGlobe(wallCorners[c][0], wallCorners[c][1], 32, 108);
  }
}

// Reusable Horizontal Compound Wall Segment along X (masonry base + pilasters + iron grille)
void drawCompoundWallRunX(float x1, float x2, float z) {
  float w = abs(x2 - x1);
  if (w < 10) return;
  float cx = (x1 + x2) * 0.5;

  // Solid textured cream masonry dado wall (y = -58..0)
  texturedWallAt(cx, -29, z, w, 58, 16, CREAM_BRIGHT, CREAM_SHADE);
  // Terracotta-rose & ivory coping cap
  boxAt(cx, -60, z, w + 4, 5, 22, ROSE_VIBRANT);
  boxAt(cx, -64, z, w + 6, 4, 24, IVORY_TRIM);

  // High wrought-iron security grille above wall (y = -66..-142)
  int bars = max(4, int(w / 11));
  drawMetalGrilleFence(min(x1, x2) + 6, max(x1, x2) - 6, z, -66, -142, bars);

  // Intermediate masonry piers with spherical cream finials every ~180 units
  int spans = max(1, int(w / 180));
  for (int i = 1; i < spans; i++) {
    float px = lerp(x1, x2, i / (float)spans);
    drawCompoundPierWithGlobe(px, z, 24, 96);
  }
}

// Reusable Depth Compound Wall Segment along Z (masonry base + pilasters + iron grille)
void drawCompoundWallRunZ(float x, float z1, float z2) {
  float d = abs(z2 - z1);
  if (d < 10) return;
  float cz = (z1 + z2) * 0.5;

  // Solid textured cream masonry dado wall (y = -58..0)
  texturedWallAt(x, -29, cz, 16, 58, d, CREAM_BRIGHT, CREAM_SHADE);
  boxAt(x, -60, cz, 22, 5, d + 4, ROSE_VIBRANT);
  boxAt(x, -64, cz, 24, 4, d + 6, IVORY_TRIM);

  // Wrought-iron security grille along Z
  boxAt(x, -72,  cz, 3, 3, d - 12, STEEL_DARK);
  boxAt(x, -104, cz, 3, 3, d - 12, STEEL_DARK);
  boxAt(x, -134, cz, 3, 3, d - 12, STEEL_DARK);
  int bars = max(6, int(d / 12));
  for (int i = 1; i < bars; i++) {
    float bz = lerp(min(z1, z2) + 6, max(z1, z2) - 6, i / (float)bars);
    boxAt(x, -104, bz, 2.5, 76, 2.5, STEEL_DARK);
  }

  // Intermediate masonry piers with spherical cream finials every ~210 units
  int spans = max(1, int(d / 210));
  for (int i = 1; i < spans; i++) {
    float pz = lerp(z1, z2, i / (float)spans);
    drawCompoundPierWithGlobe(x, pz, 24, 96);
  }
}

void drawCompoundPierWithGlobe(float px, float pz, float w, float h) {
  boxAt(px, -h * 0.5, pz, w, h, w, CREAM_BRIGHT);
  boxAt(px, -h - 3,   pz, w + 6, 6, w + 6, ROSE_VIBRANT);
  boxAt(px, -h - 7,   pz, w + 8, 4, w + 8, IVORY_TRIM);
  boxAt(px, -4,       pz, w + 5, 8, w + 5, CREAM_GOLD);

  pushMatrix();
  translate(px, -h - 19, pz);
  noStroke();
  fill(nightMode ? 0xFFFFF2B2 : IVORY_TRIM);
  sphereDetail(14);
  sphere(12.5);
  popMatrix();
}

void drawLibraryMainGate(float cx, float zWall) {
  for (int side = -1; side <= 1; side += 2) {
    float px = cx + side * 124;
    drawCompoundPierWithGlobe(px, zWall, 28, 92);
  }
  drawEntranceMetalGate(cx, zWall, 220, 82);
  drawLibrarySignboard(cx + 24, -76, zWall + 8, 170, 46);
}

// ============================================================
// GRAND SLOPED-ROOF GOPURAM / ARCHWAY ENTRANCE GATE (ON SIDE)
// Modeled directly from the reference entrance gate photograph:
//   - Wide open portal framed by paired rose-terracotta square piers
//   - Deep terracotta tiled/sloped canopy roof overhanging front & back
//   - Crowned with a cream balustrade parapet & central circular
//     Ramakrishna Math Emblem medallion crest
//   - Notice/information board mounted on the right-hand pier bay
//   - Open wrought-iron side gates & festive glowing light strings
// ============================================================

void drawGrandSideEntranceArchway(float cx, float zWall) {
  pushMatrix();
  translate(cx, 0, zWall);

  // 1. Massive Paired Terracotta-Rose Entrance Piers (Left & Right of Portal)
  //    Main portal clear opening: x = -112 to +112 (224 units wide), height = 185
  float[] pierOffsets = {-132, 118, 146};
  for (int i = 0; i < pierOffsets.length; i++) {
    float px = pierOffsets[i];
    // Stepped plinth base
    boxAt(px, -8,  0, 26, 16, 34, ROSE_DEEP);
    // Tall terracotta-rose pier shaft with vertical cream recessed panel stripes
    boxAt(px, -98, 0, 22, 166, 28, ROSE_VIBRANT);
    boxAt(px, -98, 14.5, 8, 146, 2, IVORY_TRIM);
    boxAt(px, -98, -14.5, 8, 146, 2, IVORY_TRIM);
    // Stepped capital & bracket corbels
    boxAt(px, -184, 0, 28, 8, 34, IVORY_TRIM);
    boxAt(px, -190, 0, 32, 6, 38, ROSE_VIBRANT);
  }

  // Side connector bay between the two right piers (x = +118 to +146)
  boxAt(132, -98, 0, 22, 166, 16, CREAM_BRIGHT);

  // 2. Horizontal Lintels & Deep Sloped Terracotta Canopy Roof (y = -192 to -236)
  //    Main portal lintel beam
  boxAt(0, -194, 0, 308, 14, 34, CREAM_BRIGHT);
  boxAt(0, -186, 0, 226, 5,  30, IVORY_TRIM);

  // Sloped Terracotta-Rose Canopy Roof (Chhajja / Gopuram Eave) overhanging front & rear
  for (int tier = 0; tier < 6; tier++) {
    float t = tier / 5.0;
    float ry = lerp(-198, -234, t);
    float rw = lerp(332, 296, t);
    float rd = lerp(74, 34, t);
    int roofCol = (tier % 2 == 0) ? PINK_MAIN : ROSE_VIBRANT;
    boxAt(0, ry, 0, rw, 7, rd, roofCol);
  }
  // Crisp cream eave fascia trim along front & rear roof edge
  boxAt(0, -197,  36, 334, 5, 4, IVORY_TRIM);
  boxAt(0, -197, -36, 334, 5, 4, IVORY_TRIM);

  // 3. Top Cream Balustrade Parapet & Central Circular Ramakrishna Emblem Crest
  float yParaBase = -238;
  float yParaTop  = -266;
  // Left & Right Balustrade Wings flanking the central Emblem crest
  for (int wing = -1; wing <= 1; wing += 2) {
    float wx = wing * 88;
    boxAt(wx, yParaBase - 2, 0, 114, 5, 16, CREAM_BRIGHT);
    boxAt(wx, yParaTop,      0, 118, 5, 18, IVORY_TRIM);
    for (int b = -4; b <= 4; b++) {
      float bx = wx + b * 11.5;
      cylinderYTapered(bx, (yParaBase + yParaTop) * 0.5, 0, 2.8, 2.3, 22, 8, IVORY_TRIM);
    }
    // End finial posts
    boxAt(wx + wing * 56, (yParaBase + yParaTop) * 0.5 - 3, 0, 10, 34, 14, CREAM_BRIGHT);
    sphereAt(wx + wing * 56, yParaTop - 9, 0, 6.5, IVORY_TRIM);
  }

  // Central Arched Emblem Crest Pediment (x = 0, y = -268)
  boxAt(0, -254, 0, 56, 34, 18, CREAM_BRIGHT);
  cylinderYTapered(0, -270, 0, 26, 26, 18, 24, IVORY_TRIM); // Circular crest drum rotated below
  pushMatrix();
  translate(0, -268, 0);
  rotateX(HALF_PI);
  cylinderYTapered(0, 0, 0, 26, 26, 18, 24, IVORY_TRIM);
  popMatrix();

  // Detailed Ramakrishna Order Emblem Medallion on both front & rear faces
  for (int f = -1; f <= 1; f += 2) {
    pushMatrix();
    translate(0, -268, f * 10);
    noStroke();
    fill(ROSE_DEEP);
    ellipse(0, 0, 42, 42);
    fill(EMBLEM_BLUE);
    ellipse(0, 0, 30, 30);
    fill(EMBLEM_SUN);
    arc(0, 3, 20, 20, PI, TWO_PI);
    fill(WHITE);
    ellipse(0, 4, 12, 6);
    popMatrix();
  }
  // Top Kalasha finial atop central Emblem crest
  sphereAt(0, -298, 0, 6.5, SIGN_YELLOW);

  // 4. Mounted Information / Schedule Notice Board on Right Pier (matching Photo)
  pushMatrix();
  translate(126, -86, 17);
  boxAt(0, 0, 0, 68, 52, 3, IVORY_TRIM);
  boxAt(0, -18, 2, 64, 10, 1.5, SIGN_RED);
  boxAt(0,   4, 2, 64, 28, 1.5, SIGN_WHITE);
  boxAt(0,  21, 2, 64,  6, 1.5, SIGN_BLUE);
  popMatrix();

  // 5. Open Wrought-Iron Folding Side Gates tucked beside the piers
  drawEntranceMetalGate(-102, 0, 22, 130);
  drawEntranceMetalGate( 100, 0, 22, 130);

  // 6. Festive Illuminated String Lights draped under the Archway Eave & Piers
  noStroke();
  int bulbCol = nightMode ? 0xFFFFE875 : 0xFFFFD84D;
  fill(bulbCol);
  // Horizontal festoon garlands across the portal lintel
  for (int g = 0; g < 3; g++) {
    float xStart = -120 + g * 80;
    for (int i = 0; i <= 10; i++) {
      float t = i / 10.0;
      float bx = xStart + t * 80;
      float sag = sin(t * PI) * 16;
      sphereAt(bx, -184 + sag, 26, 2.2, bulbCol);
    }
  }
  // Vertical light strings along the right entrance piers
  for (float by = -180; by <= -30; by += 14) {
    sphereAt(114, by, 18, 2.0, bulbCol);
    sphereAt(142, by, 18, 2.0, bulbCol);
  }

  popMatrix();
}

void sphereAt(float x, float y, float z, float r, int c) {
  pushMatrix();
  translate(x, y, z);
  noStroke();
  fill(c);
  sphereDetail(8);
  sphere(r);
  popMatrix();
}

void drawMetalGrilleFence(float x1, float x2, float z, float yBot, float yTop, int bars) {
  float w = x2 - x1;
  float cx = (x1 + x2) * 0.5;
  float h = abs(yTop - yBot);
  float cy = (yBot + yTop) * 0.5;

  boxAt(cx, yBot + 6,  z, w, 3, 3, STEEL_DARK);
  boxAt(cx, cy,        z, w, 3, 3, STEEL_DARK);
  boxAt(cx, yTop + 12, z, w, 3, 3, STEEL_DARK);

  for (int i = 0; i <= bars; i++) {
    float bx = lerp(x1, x2, i / (float)bars);
    boxAt(bx, cy, z, 2.5, h, 2.5, STEEL_DARK);
  }
}

void drawEntranceMetalGate(float cx, float z, float w, float h) {
  for (int leaf = -1; leaf <= 1; leaf += 2) {
    float lx = cx + leaf * (w * 0.25);
    float lw = w * 0.49;

    boxAt(lx, -h * 0.5, z, lw, 3.5, 3.5, STEEL_DARK);
    boxAt(lx, -4,       z, lw, 3.5, 3.5, STEEL_DARK);
    boxAt(lx, -h + 4,   z, lw, 3.5, 3.5, STEEL_DARK);
    boxAt(lx - lw * 0.48, -h * 0.5, z, 3.5, h, 3.5, STEEL_DARK);
    boxAt(lx + lw * 0.48, -h * 0.5, z, 3.5, h, 3.5, STEEL_DARK);

    for (float hy = -12; hy >= -h * 0.48; hy -= 8) {
      boxAt(lx, hy, z, lw - 4, 2.2, 2.2, STEEL_DARK);
    }
    for (float vx = lx - lw * 0.42; vx <= lx + lw * 0.42; vx += 10) {
      boxAt(vx, -h * 0.5, z, 2.0, h - 6, 2.0, STEEL_DARK);
    }
  }
}

// ============================================================
// LUSH CAMPUS TREES & VIVID GREEN COURTYARD PLANTER BEDS
// ============================================================

void drawLushCampusTreesAndPlanters() {
  // Tall neem/banyan trees framing the courtyard plaza, side archway & temple
  drawTree(-310, 0,  -40, 310, 115);
  drawTree(-320, 0, -240, 340, 125);
  drawTree(-690, 0,  110, 300, 115);
  drawTree(-690, 0, -560, 320, 120);
  drawTree(-690, 0, -780, 340, 130);
  drawTree( 290, 0, -140, 300, 110);
  drawTree( 290, 0, -380, 320, 120);

  // Vivid Green Rectangular Planter Beds on the Left Courtyard
  drawPlanterBed(-310, -110, 75, 115);
  drawPlanterBed(-310, -310, 75, 115);

  // Shrubs inside front compound wall
  drawShrub(-210, 0, 195, 1.15);
  drawShrub( 210, 0, 195, 1.15);
  drawShrub(-670, 0, 195, 1.15);
}

void drawPlanterBed(float cx, float cz, float w, float d) {
  boxAt(cx, -7, cz, w, 14, d, POT_GREEN);
  boxAt(cx, -15, cz, w - 4, 4, d - 4, LEAF_VIVID);
  for (float px = cx - w * 0.36; px <= cx + w * 0.36; px += 18) {
    for (float pz = cz - d * 0.40; pz <= cz + d * 0.40; pz += 18) {
      boxAt(px, -18, pz, 12, 8, 12, LEAF_LIME);
    }
  }
}

void drawTree(float x, float y, float z, float h, float r) {
  stroke(TRUNK_BROWN);
  strokeWeight(14);
  line(x, y, z, x, y - h * 0.65, z);
  noStroke();

  pushMatrix();
  translate(x, y - h * 0.72, z);
  sphereDetail(7);
  fill(LEAF_DEEP);
  sphere(r * 0.85);
  translate(-r * 0.38, 16, r * 0.25);
  fill(LEAF_VIVID);
  sphere(r * 0.68);
  translate(r * 0.78, -10, -r * 0.30);
  fill(LEAF_LIME);
  sphere(r * 0.60);
  popMatrix();
}

void drawShrub(float x, float y, float z, float s) {
  pushMatrix();
  translate(x, y - 14 * s, z);
  scale(s);
  noStroke();
  fill(LEAF_DEEP);
  sphereDetail(6);
  sphere(18);
  translate(14, -4, 4);
  fill(LEAF_VIVID);
  sphere(15);
  translate(-26, -3, -2);
  fill(LEAF_LIME);
  sphere(13);
  popMatrix();
}

// ============================================================
// SRI RAMAKRISHNA MATH UNIVERSAL TEMPLE (BEHIND COURTYARD)
// Aligned at x = -460, z = -700 directly through the side entrance arch!
// ============================================================

void drawUniversalTempleBehind() {
  pushMatrix();
  translate(-460, 0, -700);
  scale(0.88);

  boxAt(0, -1, 145, 360, 2, 220, MARBLE_WHITE);
  stroke(MARBLE_GREY);
  strokeWeight(1.1);
  for (float x = -170; x <= 170; x += 34) {
    line(x, -2.2, 40, x, -2.2, 250);
  }
  for (float z = 45; z <= 245; z += 30) {
    line(-175, -2.2, z, 175, -2.2, z);
  }
  noStroke();

  boxAt(0, -55, -105, 236, 110, 320, PINK_MAIN);
  boxAt(0, -4,   -105, 246, 8, 330, PINK_DARK);
  boxAt(0, -110, -105, 248, 8, 332, CREAM_TEMPLE);
  boxAt(0, -115, -105, 242, 4, 326, PINK_LIGHT);
  boxAt(0, -165, -95,  188, 100, 300, PINK_MAIN);
  drawTempleBarrelVaultNave();

  drawTempleSanctumShikhara();
  drawTempleFourCornerTowers();
  drawTempleChaityaFacadeAndEmblem();
  drawTempleEntrancePorchAndStairs();

  drawTempleDravidianShrine(-88, 152);
  drawTempleDravidianShrine( 88, 152);

  drawTempleBalustradeWall(-210, 260, -42, 260, 12);
  drawTempleBalustradeWall(  42, 260, 210, 260, 12);
  drawTempleParapetKiosk(-210, 260);
  drawTempleParapetKiosk( 210, 260);
  drawTempleParapetKiosk( -46, 260);
  drawTempleParapetKiosk(  46, 260);

  popMatrix();
}

void drawTempleDome(float x, float yBase, float z, float rx, float ry, float rz, int domeCol, int trimCol) {
  pushMatrix();
  translate(x, yBase, z);
  noStroke();

  fill(trimCol);
  box(rx * 2.15, 7, rz * 2.15);

  int rings = 14;
  int segs = 24;
  fill(domeCol);

  for (int r = 0; r < rings; r++) {
    float t0 = r / (float)rings;
    float t1 = (r + 1) / (float)rings;
    float phi0 = t0 * HALF_PI;
    float phi1 = t1 * HALF_PI;

    float rad0 = cos(phi0) * (1.0 + 0.08 * sin(phi0 * 2.0));
    float rad1 = cos(phi1) * (1.0 + 0.08 * sin(phi1 * 2.0));
    float yy0 = -sin(phi0) * ry;
    float yy1 = -sin(phi1) * ry;

    beginShape(QUAD_STRIP);
    for (int s = 0; s <= segs; s++) {
      float th = s * TWO_PI / segs;
      float cth = cos(th);
      float sth = sin(th);
      normal(cth * cos(phi0), -sin(phi0), sth * cos(phi0));
      vertex(cth * rx * rad0, yy0, sth * rz * rad0);
      normal(cth * cos(phi1), -sin(phi1), sth * cos(phi1));
      vertex(cth * rx * rad1, yy1, sth * rz * rad1);
    }
    endShape();
  }

  translate(0, -ry, 0);
  fill(trimCol);
  sphereDetail(10);
  sphere(rx * 0.16);
  translate(0, -rx * 0.16, 0);
  sphere(rx * 0.10);
  boxAt(0, -rx * 0.14, 0, 2.5, rx * 0.24, 2.5, trimCol);

  popMatrix();
}

void drawTempleBarrelVaultNave() {
  float zFront = 55;
  float zBack  = -245;
  float ySpring = -215;
  float rx = 96;
  float ry = 104;
  int segs = 28;

  noStroke();
  fill(PINK_LIGHT);
  beginShape(QUAD_STRIP);
  for (int i = 0; i <= segs; i++) {
    float a = PI + i * PI / segs;
    float vx = cos(a) * rx;
    float vy = ySpring + sin(a) * ry;
    normal(cos(a), sin(a), 0);
    vertex(vx, vy, zFront);
    vertex(vx, vy, zBack);
  }
  endShape();

  boxAt(0, ySpring - ry - 3, (zFront + zBack) * 0.5, 10, 7, abs(zFront - zBack), CREAM_TEMPLE);
  for (float z = zBack + 12; z <= zFront - 12; z += 16) {
    boxAt(0, ySpring - ry - 8, z, 5, 6, 6, IVORY_TRIM);
  }

  for (float z = -215; z <= 35; z += 35) {
    for (int s = -1; s <= 1; s += 2) {
      boxAt(s * 96, -218, z, 14, 24, 16, PINK_MAIN);
      boxAt(s * 97, -231, z, 16, 5, 18, CREAM_TEMPLE);
      drawTempleDome(s * 96, -233, z, 7, 10, 8, PINK_LIGHT, CREAM_TEMPLE);
    }
  }
}

void drawTempleSanctumShikhara() {
  float cz = -195;
  float yBase = -215;
  float h = 195;
  int levels = 18;
  int segs = 28;

  boxAt(0, -225, cz, 142, 36, 142, PINK_MAIN);
  boxAt(0, -244, cz, 148, 6, 148, CREAM_TEMPLE);

  noStroke();
  for (int l = 0; l < levels; l++) {
    float t0 = l / (float)levels;
    float t1 = (l + 1) / (float)levels;
    float y0 = yBase - 28 - t0 * (h - 28);
    float y1 = yBase - 28 - t1 * (h - 28);

    float r0 = 68 * pow(1.0 - t0 * 0.76, 0.62);
    float r1 = 68 * pow(1.0 - t1 * 0.76, 0.62);

    beginShape(QUAD_STRIP);
    for (int s = 0; s <= segs; s++) {
      float a = s * TWO_PI / segs;
      float flute = 1.0 + 0.045 * cos(a * 16.0);
      float ca = cos(a);
      float sa = sin(a);
      fill((s % 2 == 0) ? PINK_MAIN : PINK_LIGHT);
      normal(ca, -0.25, sa);
      vertex(ca * r0 * flute, y0, cz + sa * r0 * flute);
      vertex(ca * r1 * flute, y1, cz + sa * r1 * flute);
    }
    endShape();
  }

  float yTop = yBase - h;
  cylinderYTapered(0, yTop - 4, cz, 22, 20, 8, 20, CREAM_TEMPLE);
  for (int i = 0; i < 16; i++) {
    float a = i * TWO_PI / 16.0;
    pushMatrix();
    translate(cos(a) * 23, yTop - 11, cz + sin(a) * 23);
    fill(PINK_LIGHT);
    sphereDetail(6);
    sphere(6.5);
    popMatrix();
  }
  cylinderYTapered(0, yTop - 11, cz, 24, 24, 9, 20, CREAM_SHADE);
  drawTempleDome(0, yTop - 16, cz, 18, 16, 18, CREAM_TEMPLE, IVORY_TRIM);
  boxAt(0, yTop - 42, cz, 2.5, 24, 2.5, IVORY_TRIM);
}

void drawTempleFourCornerTowers() {
  float[][] corners = {
    {-126,  35},
    { 126,  35},
    {-126, -245},
    { 126, -245}
  };

  for (int i = 0; i < corners.length; i++) {
    float tx = corners[i][0];
    float tz = corners[i][1];

    boxAt(tx, -119, tz, 64, 238, 64, PINK_MAIN);
    boxAt(tx, -4,   tz, 72, 8, 72, PINK_DARK);
    boxAt(tx, -110, tz, 72, 7, 72, CREAM_TEMPLE);
    boxAt(tx, -182, tz, 70, 6, 70, CREAM_SHADE);
    boxAt(tx, -238, tz, 74, 8, 74, CREAM_TEMPLE);

    boxAt(tx, -206, tz + (tz > 0 ? 33 : -33), 24, 24, 3, CREAM_SHADE);
    boxAt(tx + (tx > 0 ? 33 : -33), -206, tz, 3, 24, 24, CREAM_SHADE);

    for (int cx = -1; cx <= 1; cx += 2) {
      for (int cz = -1; cz <= 1; cz += 2) {
        boxAt(tx + cx * 33, -232, tz + cz * 33, 12, 14, 12, IVORY_TRIM);
      }
    }

    drawTempleDome(tx, -242, tz, 31, 38, 31, PINK_LIGHT, CREAM_TEMPLE);
  }

  boxAt(-126, -68, 68, 44, 30, 3, BLACK_PLAQUE);
  boxAt(-126, -68, 67.5, 48, 34, 2, CREAM_SHADE);

  for (int side = -1; side <= 1; side += 2) {
    float wx = side * 80;
    boxAt(wx, -152, 57, 18, 26, 4, WOOD_TEAK);
    boxAt(wx, -152, 58, 24, 32, 3, CREAM_TEMPLE);
    boxAt(wx, -171, 59, 26, 6, 5, IVORY_TRIM);

    pushMatrix();
    translate(side * 126, -138, 68);
    fill(CREAM_TEMPLE);
    ellipse(0, 0, 16, 16);
    popMatrix();
  }
}

void drawTempleChaityaFacadeAndEmblem() {
  float zFace = 57;
  float ySpring = -215;
  float rx = 96;
  float ry = 104;

  noStroke();
  fill(PINK_MAIN);
  beginShape(TRIANGLE_FAN);
  normal(0, 0, 1);
  vertex(0, ySpring + 6, zFace);
  for (int i = 0; i <= 32; i++) {
    float a = PI + i * PI / 32.0;
    vertex(cos(a) * rx, ySpring + sin(a) * ry, zFace);
  }
  endShape();

  int segs = 36;
  for (int i = 0; i < segs; i++) {
    float a0 = PI + i * PI / segs;
    float a1 = PI + (i + 1) * PI / segs;
    float xm = cos((a0 + a1) * 0.5) * (rx + 4);
    float ym = ySpring + sin((a0 + a1) * 0.5) * (ry + 4);
    float ang = atan2(sin(a1) * ry - sin(a0) * ry, cos(a1) * rx - cos(a0) * rx);

    pushMatrix();
    translate(xm, ym, zFace + 5);
    rotateZ(ang);
    boxAt(0, 0, 0, 14, 14, 14, IVORY_TRIM);
    boxAt(0, 7, 1, 14, 4, 15, PINK_LIGHT);
    popMatrix();
  }

  float apexY = ySpring - ry - 14;
  boxAt(0, apexY, zFace + 6, 18, 24, 12, IVORY_TRIM);
  pushMatrix();
  translate(0, apexY - 8, zFace + 8);
  fill(CREAM_TEMPLE);
  sphereDetail(10);
  sphere(10);
  popMatrix();

  for (int side = -1; side <= 1; side += 2) {
    boxAt(side * (rx + 4), ySpring + 4, zFace + 6, 22, 32, 16, IVORY_TRIM);
    boxAt(side * (rx + 8), ySpring + 12, zFace + 8, 16, 16, 14, CREAM_SHADE);
  }

  for (int k = -4; k <= 4; k++) {
    float dx = k * 16.5;
    float topArchY = ySpring - ry * sqrt(max(0.05, 1.0 - (dx / (rx - 8)) * (dx / (rx - 8)))) + 10;
    float botDropY = (abs(k) <= 1) ? -266 : -232;
    if (botDropY > topArchY + 6) {
      float hDrop = botDropY - topArchY;
      boxAt(dx, topArchY + hDrop * 0.5, zFace + 3, 5, hDrop, 5, CREAM_TEMPLE);
      for (float by = topArchY + 6; by <= botDropY; by += 11) {
        boxAt(dx, by, zFace + 5, 8, 5, 6, IVORY_TRIM);
      }
    }
  }

  float ey = -236;
  float ez = zFace + 6;
  pushMatrix();
  translate(0, ey, ez);

  noStroke();
  fill(IVORY_TRIM);
  ellipse(0, 0, 56, 56);
  fill(PINK_DEEP);
  ellipse(0, 0, 44, 44);

  stroke(IVORY_TRIM);
  strokeWeight(2.0);
  for (int s = 0; s < 16; s++) {
    float a = s * TWO_PI / 16.0;
    line(cos(a) * 11, sin(a) * 11, cos(a) * 21, sin(a) * 21);
  }
  noStroke();

  fill(nightMode ? 0xFF56B4F5 : EMBLEM_BLUE);
  ellipse(0, 0, 24, 24);
  fill(EMBLEM_SUN);
  arc(0, 2, 16, 16, PI, TWO_PI);
  fill(WHITE);
  ellipse(0, 3, 10, 5);

  popMatrix();
}

void drawTempleEntrancePorchAndStairs() {
  boxAt(0, -55, 80, 74, 110, 50, PINK_MAIN);
  boxAt(0, -110, 80, 78, 6, 54, MARBLE_WHITE);

  boxAt(0, -155, 58, 46, 86, 8, 0xFF1A0E0C);
  if (nightMode) {
    boxAt(0, -145, 54, 18, 24, 4, 0xFFFFD275);
  }
  boxAt(0, -155, 60, 54, 92, 4, CREAM_TEMPLE);

  boxAt(0, -202, 84, 84, 10, 54, PINK_LIGHT);
  boxAt(0, -208, 84, 88, 6, 58, IVORY_TRIM);
  boxAt(0, -196, 84, 78, 5, 48, CREAM_SHADE);
  for (float kx = -32; kx <= 32; kx += 16) {
    boxAt(kx, -213, 111, 8, 7, 5, IVORY_TRIM);
  }

  for (int side = -1; side <= 1; side += 2) {
    float px = side * 28;
    float pz = 100;
    boxAt(px, -122, pz, 15, 24, 18, IVORY_TRIM);
    boxAt(px, -128, pz + 6, 12, 12, 10, CREAM_TEMPLE);
    cylinderYTapered(px, -165, pz, 6.5, 5.5, 62, 14, PINK_LIGHT);
    cylinderYTapered(px, -192, pz, 9.5, 11.0, 8, 14, IVORY_TRIM);
  }

  int numSteps = 22;
  float zTop = 105;
  float zBot = 255;
  float yTop = -110;
  float yBot = 0;

  float stepH = (yBot - yTop) / numSteps;
  float stepD = (zBot - zTop) / numSteps;
  for (int i = 0; i < numSteps; i++) {
    float sy = yTop + (i + 0.5) * stepH;
    float sz = zTop + (i + 0.5) * stepD;
    boxAt(0, (sy + yBot) * 0.5, sz, 70, yBot - sy, stepD + 1, PINK_DARK);
    int treadCol = (i % 3 == 1) ? 0xFFD6D8DE : MARBLE_WHITE;
    boxAt(0, sy - stepH * 0.45, sz, 72, 3.2, stepD + 2.5, treadCol);
  }

  for (int side = -1; side <= 1; side += 2) {
    float bx = side * 38;
    for (int i = 0; i <= 14; i++) {
      float t = i / 14.0;
      float sy = lerp(yTop, yBot, t);
      float sz = lerp(zTop, zBot, t);
      boxAt(bx, sy - 4, sz, 8, 10, 14, PINK_MAIN);
      cylinderYTapered(bx, sy - 16, sz, 2.5, 2.0, 16, 8, IVORY_TRIM);
      boxAt(bx, sy - 26, sz, 10, 5, 14, PINK_LIGHT);
    }
    boxAt(bx, -18, zBot + 4, 14, 36, 14, PINK_MAIN);
    drawTempleDome(bx, -36, zBot + 4, 7, 9, 7, PINK_LIGHT, CREAM_TEMPLE);
  }
}

void drawTempleDravidianShrine(float sx, float sz) {
  pushMatrix();
  translate(sx, 0, sz);

  boxAt(0, -6,  0, 68, 12, 68, PINK_DARK);
  boxAt(0, -18, 0, 60, 12, 60, PINK_MAIN);
  boxAt(0, -25, 0, 64, 4,  64, CREAM_TEMPLE);

  boxAt(0, -51, 0, 52, 50, 52, PINK_MAIN);
  boxAt(0, -48, 26, 22, 38, 4, WOOD_TEAK);
  boxAt(0, -68, 27, 28, 7, 6, IVORY_TRIM);
  for (int p = -1; p <= 1; p += 2) {
    boxAt(p * 13, -48, 27, 4, 38, 4, CREAM_TEMPLE);
  }

  boxAt(0, -79, 0, 62, 7, 62, IVORY_TRIM);

  boxAt(0, -92, 0, 44, 20, 44, PINK_MAIN);
  boxAt(0, -103, 0, 48, 5, 48, CREAM_TEMPLE);
  for (int cx = -1; cx <= 1; cx += 2) {
    for (int cz = -1; cz <= 1; cz += 2) {
      boxAt(cx * 21, -94, cz * 21, 9, 14, 9, IVORY_TRIM);
    }
  }
  boxAt(0, -94, 23, 14, 14, 7, IVORY_TRIM);

  boxAt(0, -111, 0, 30, 13, 30, PINK_LIGHT);
  boxAt(0, -119, 0, 34, 4,  34, CREAM_TEMPLE);

  drawTempleDome(0, -121, 0, 17, 22, 17, PINK_LIGHT, IVORY_TRIM);

  popMatrix();
}

void drawTempleBalustradeWall(float x1, float z1, float x2, float z2, int count) {
  float dx = x2 - x1;
  float dz = z2 - z1;
  float len = sqrt(dx * dx + dz * dz);
  float ang = -atan2(dz, dx);
  float cx = (x1 + x2) * 0.5;
  float cz = (z1 + z2) * 0.5;

  pushMatrix();
  translate(cx, 0, cz);
  rotateY(ang);

  boxAt(0, -9, 0, len, 18, 10, PINK_MAIN);
  boxAt(0, -19, 0, len + 2, 4, 13, CREAM_TEMPLE);
  boxAt(0, -42, 0, len + 2, 6, 13, PINK_LIGHT);
  boxAt(0, -39, 0, len, 3, 11, CREAM_SHADE);

  for (int i = 1; i < count; i++) {
    float bx = lerp(-len * 0.5, len * 0.5, i / (float)count);
    cylinderYTapered(bx, -29, 0, 3.0, 2.4, 18, 8, IVORY_TRIM);
    boxAt(bx, -29, 0, 7.5, 4, 7.5, CREAM_TEMPLE);
  }

  popMatrix();
}

void drawTempleParapetKiosk(float kx, float kz) {
  pushMatrix();
  translate(kx, 0, kz);
  boxAt(0, -24, 0, 30, 48, 30, PINK_MAIN);
  boxAt(0, -24, 0, 12, 22, 32, WOOD_TEAK);
  boxAt(0, -50, 0, 36, 6, 36, IVORY_TRIM);
  drawTempleDome(0, -53, 0, 15, 18, 15, PINK_LIGHT, CREAM_TEMPLE);
  popMatrix();
}

// ============================================================
// INTERACTIVE TOUCH HUD & CAMERA CONTROLS
// ============================================================

float panelW() {
  return min(304, width * 0.47);
}

float panelH() {
  return min(214, height - 24);
}

void drawMiniHudButton() {
  hint(DISABLE_DEPTH_TEST);
  camera();
  noLights();

  fill(20, 28, 42, 230);
  stroke(IVORY_TRIM);
  strokeWeight(1.4);
  rect(12, 12, 64, 28, 8);

  noStroke();
  fill(IVORY_TRIM);
  textAlign(CENTER, CENTER);
  textSize(10.5);
  text("HUD", 44, 26);

  hint(ENABLE_DEPTH_TEST);
}

void drawHUD() {
  hint(DISABLE_DEPTH_TEST);
  camera();
  noLights();

  float x = 12;
  float y = 12;
  float w = panelW();
  float h = panelH();

  noStroke();
  fill(20, 28, 42, 236);
  rect(x, y, w, h, 12);

  stroke(IVORY_TRIM);
  strokeWeight(1.5);
  noFill();
  rect(x, y, w, h, 12);
  noStroke();

  fill(IVORY_TRIM);
  textAlign(LEFT, TOP);
  textSize(11.5);
  text("SRI RAMAKRISHNA MATH 3D CAMPUS", x + 12, y + 10);

  fill(ROSE_VIBRANT);
  textSize(8.4);
  text("ENCLOSED COMPOUND, ARCH GATE & 3-STOREY", x + 12, y + 25);

  fill(46, 58, 78);
  stroke(CREAM_GOLD);
  strokeWeight(1);
  rect(x + w - 48, y + 10, 36, 18, 5);
  noStroke();
  fill(WHITE);
  textAlign(CENTER, CENTER);
  textSize(8.5);
  text("HIDE", x + w - 30, y + 19);

  float bw = (w - 30) / 2.0;
  float bh = 25;

  // Row 1: Side Entrance Arch | 3-Storey Library View
  drawBtn("ENTRANCE ARCH VIEW", x + 12, y + 44, bw, bh, 0xFF9C3E46);
  drawBtn("3-STOREY LIBRARY",   x + 18 + bw, y + 44, bw, bh, 0xFF9C3E46);

  // Row 2: Courtyard + Temple | Reset Camera
  drawBtn("COURTYARD + TEMPLE", x + 12, y + 75, bw, bh, 0xFF7A4445);
  drawBtn("RESET CAMERA",       x + 18 + bw, y + 75, bw, bh, 0xFF3E5068);

  // Row 3: Daylight / Night Lighting | Zoom +
  drawBtn(nightMode ? "VIBRANT DAYLIGHT" : "NIGHT FLOODLIT", x + 12, y + 106, bw, bh, nightMode ? 0xFFC77B22 : 0xFF235284);
  drawBtn("ZOOM +",             x + 18 + bw, y + 106, bw, bh, 0xFF344458);

  // Row 4: Zoom - | Full Compound Aerial
  drawBtn("ZOOM -",             x + 12, y + 137, bw, bh, 0xFF344458);
  drawBtn("FULL COMPOUND VIEW", x + 18 + bw, y + 137, bw, bh, 0xFF2D5E46);

  fill(CREAM_GOLD);
  textAlign(LEFT, TOP);
  textSize(8.0);
  text("Drag screen to orbit 360 | +/-: Zoom", x + 12, y + 172);
  text("Keys: N=Night/Day, R=Reset, H=Toggle HUD", x + 12, y + 185);

  hint(ENABLE_DEPTH_TEST);
}

void drawBtn(String label, float bx, float by, float bw, float bh, int col) {
  fill(col);
  stroke(CREAM_GOLD);
  strokeWeight(1.1);
  rect(bx, by, bw, bh, 6);
  noStroke();
  fill(IVORY_TRIM);
  textAlign(CENTER, CENTER);
  textSize(8.2);
  text(label, bx + bw * 0.5, by + bh * 0.5);
}

// ============================================================
// TOUCH & MOUSE INTERACTION
// ============================================================

void mousePressed() {
  previousMouseX = mouseX;
  previousMouseY = mouseY;

  if (!showHUD) {
    if (mouseX >= 12 && mouseX <= 76 && mouseY >= 12 && mouseY <= 40) {
      showHUD = true;
    }
    return;
  }

  float x = 12;
  float y = 12;
  float w = panelW();
  float h = panelH();

  if (mouseX < x || mouseX > x + w || mouseY < y || mouseY > y + h) {
    return;
  }

  if (mouseX >= x + w - 48 && mouseX <= x + w - 12 &&
      mouseY >= y + 10 && mouseY <= y + 28) {
    showHUD = false;
    return;
  }

  float bw = (w - 30) / 2.0;
  float bh = 25;

  // Row 1
  if (mouseY >= y + 44 && mouseY <= y + 44 + bh) {
    if (mouseX >= x + 12 && mouseX <= x + 12 + bw) {
      // View looking straight through the new Side Entrance Archway toward the Temple
      targetYaw = -0.14;
      targetPitch = 0.08;
      targetZoom = 0.90;
      return;
    }
    if (mouseX >= x + 18 + bw && mouseX <= x + 18 + bw * 2) {
      // 3-Storey Library Front Gate View
      targetYaw = 0.16;
      targetPitch = 0.06;
      targetZoom = 0.85;
      return;
    }
  }

  // Row 2
  if (mouseY >= y + 75 && mouseY <= y + 75 + bh) {
    if (mouseX >= x + 12 && mouseX <= x + 12 + bw) {
      // Inside Courtyard looking at 3-Storey Wing + Temple Behind
      targetYaw = -0.66;
      targetPitch = 0.14;
      targetZoom = 0.72;
      return;
    }
    if (mouseX >= x + 18 + bw && mouseX <= x + 18 + bw * 2) {
      resetCamera();
      return;
    }
  }

  // Row 3
  if (mouseY >= y + 106 && mouseY <= y + 106 + bh) {
    if (mouseX >= x + 12 && mouseX <= x + 12 + bw) {
      nightMode = !nightMode;
      return;
    }
    if (mouseX >= x + 18 + bw && mouseX <= x + 18 + bw * 2) {
      targetZoom = min(1.85, targetZoom + 0.12);
      return;
    }
  }

  // Row 4
  if (mouseY >= y + 137 && mouseY <= y + 137 + bh) {
    if (mouseX >= x + 12 && mouseX <= x + 12 + bw) {
      targetZoom = max(0.38, targetZoom - 0.12);
      return;
    }
    if (mouseX >= x + 18 + bw && mouseX <= x + 18 + bw * 2) {
      // Full Enclosed Compound Aerial View
      targetYaw = -0.48;
      targetPitch = 0.45;
      targetZoom = 0.62;
      return;
    }
  }
}

void mouseDragged() {
  if (!showHUD || mouseX > panelW() + 20 || mouseY > panelH() + 20) {
    targetYaw += (mouseX - previousMouseX) * 0.006;
    targetPitch = constrain(
      targetPitch + (mouseY - previousMouseY) * 0.004,
      -0.08,
      0.62
    );
    cameraYaw = targetYaw;
    cameraPitch = targetPitch;
  }
  previousMouseX = mouseX;
  previousMouseY = mouseY;
}

void resetCamera() {
  targetYaw   = -0.22;
  targetPitch =  0.12;
  targetZoom  =  0.72;
}

void mouseWheel(processing.event.MouseEvent event) {
  // Intentionally empty for APDE Android compatibility
}

void keyPressed() {
  if (key == 'h' || key == 'H') showHUD = !showHUD;
  if (key == 'r' || key == 'R') resetCamera();
  if (key == 'n' || key == 'N') nightMode = !nightMode;
  if (key == '+' || key == '=') targetZoom = min(1.85, targetZoom + 0.12);
  if (key == '-')                targetZoom = max(0.38, targetZoom - 0.12);
}

// ============================================================
// END OF SRI RAMAKRISHNA MATH 3D CAMPUS SKETCH
// ============================================================
