import java.util.ArrayList;

// ============================================================
// SRI RAMAKRISHNA MATH UNIVERSAL TEMPLE - 3D ARCHITECTURAL SKETCH
// COMPLETE APDE / PROCESSING JAVA MODE SKETCH
//
// Architectural Features Modeled from Reference Images:
// 1. Terracotta-rose & cream Indo-Saracenic / Chaitya-arch prayer hall
// 2. Ribbed barrel-vault nave roof with cream spine & lateral buttress ribs
// 3. Tall ribbed Shikhara / Rekha-Deul sanctum tower with amalaka & kalasha
// 4. Grand front Chaitya arch gable with cascading relief drops, Makara corner
//    brackets, and central 16-spoke circular stained-glass Emblem window
// 5. Recessed sanctum porch with ornate cornice & twin lion-base pillars
// 6. Grand marble-and-terracotta ceremonial staircase with baluster railings
// 7. Four corner square towers capped with hemispherical domes & lotus finials
// 8. Two ornate Dravidian Vimana-style flanking shrines at the base of the stairs
// 9. Sacred reflecting water tank (Kalyani / Pushkarini) surrounded by cream-baluster
//    parapet walls, corner chhatri kiosks, central viewing shrine & water ripples
// 10. Interactive APDE touch controls (Orbit, Zoom, Day/Evening Lighting, Presets)
//
// Coordinate convention:
//   X = left (-) / right (+)
//   Y = vertical (courtyard floor = 0, upward is negative, tank water = +22)
//   Z = back / sanctum (-) to front / tank (+)
// ============================================================

// ---------------- PALETTE -------------------------------

final int PINK_MAIN    = 0xFFC58281; // Signature Ramakrishna Math terracotta rose
final int PINK_LIGHT   = 0xFFD99A98;
final int PINK_DARK    = 0xFF9C5E5E;
final int PINK_DEEP    = 0xFF7A4445;

final int CREAM        = 0xFFF5E6D3; // Ornamental trim & balustrades
final int CREAM_LIGHT  = 0xFFFFF6EA;
final int CREAM_DARK   = 0xFFD9C3A9;

final int WHITE        = 0xFFFFFFFF; // Pure white highlight (fixed declaration)
final int MARBLE_WHITE = 0xFFEDEAE4;
final int MARBLE_GREY  = 0xFF8F9299;

final int EMBLEM_BLUE  = 0xFF3E84B8;
final int EMBLEM_SUN   = 0xFFF7B538;
final int WOOD_DARK    = 0xFF422417;
final int BLACK_PLAQUE = 0xFF1F2226;

final int WATER_DEEP   = 0xFF1A3A4A;
final int WATER_SURF   = 0xFF2B586C;

final int LEAF_DARK    = 0xFF1F4726;
final int LEAF_MID     = 0xFF336936;
final int LEAF_LIGHT   = 0xFF528C46;
final int TRUNK_BROWN  = 0xFF5C4332;
final int EARTH_RED    = 0xFF8C5343;

// ---------------- CAMERA & UI STATE ---------------------

float cameraYaw   = -0.34;
float cameraPitch =  0.22;
float cameraZoom  =  0.92;

float targetYaw   = -0.34;
float targetPitch =  0.22;
float targetZoom  =  0.92;

boolean eveningMode  = false;
boolean showRipples  = true;
boolean showHUD      = true;

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

  if (eveningMode) {
    background(16, 26, 48);
  } else {
    background(88, 148, 212);
  }

  pushMatrix();

  translate(width * 0.50, height * 0.62, 0);
  float fit = min(width / 1080.0, height / 900.0);
  scale(fit * cameraZoom);

  rotateX(cameraPitch);
  rotateY(cameraYaw);

  // Sky clouds in Day mode
  if (!eveningMode) {
    noLights();
    drawSkyClouds();
  }

  // Lighting Setup
  lights();
  if (eveningMode) {
    ambientLight(95, 90, 105);
    directionalLight(140, 145, 175, -0.4, 0.7, -0.5);
    pointLight(255, 200, 120, 0, -210, 45);
    pointLight(255, 190, 110, 0, -140, 260);
  } else {
    ambientLight(155, 148, 145);
    directionalLight(245, 235, 225, -0.45, 0.72, 0.52);
    directionalLight(110, 125, 145, 0.50, 0.35, -0.65);
  }

  drawGroundAndTank();
  drawMainPrayerHall();
  drawSanctumShikhara();
  drawFourCornerTowers();
  drawChaityaFacadeAndEmblem();
  drawEntrancePorchAndStairs();
  drawFlankingDravidianShrines();
  drawBalustradeCourtyardAndKiosks();
  drawSurroundingTreesAndPots();

  popMatrix();

  if (showHUD) {
    drawHUD();
  } else {
    drawMiniHudButton();
  }

  frameNumber++;
}

// ============================================================
// 3D GEOMETRY PRIMITIVES
// ============================================================

void boxAt(float x, float y, float z, float w, float h, float d, int c) {
  pushMatrix();
  translate(x, y, z);
  noStroke();
  fill(c);
  box(w, h, d);
  popMatrix();
}

void cylinderY(float x, float yCenter, float z, float rBot, float rTop, float h, int segs, int c) {
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

  // Top cap
  beginShape(TRIANGLE_FAN);
  normal(0, -1, 0);
  vertex(0, y0, 0);
  for (int i = 0; i <= segs; i++) {
    float a = i * TWO_PI / segs;
    vertex(cos(a) * rTop, y0, sin(a) * rTop);
  }
  endShape();

  // Bottom cap
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

// Hemispherical / Ogee-pointed Indian dome with lotus necking & Kalasha finial
void drawTempleDome(float x, float yBase, float z, float rx, float ry, float rz, int domeCol, int trimCol) {
  pushMatrix();
  translate(x, yBase, z);
  noStroke();

  // Cream cornice collar
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

    // Slight ogee bulge profile characteristic of Ramakrishna Math domes
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

  // Inverted lotus cap & brass/cream Kalasha finial
  translate(0, -ry, 0);
  fill(trimCol);
  sphereDetail(10);
  sphere(rx * 0.16);
  translate(0, -rx * 0.16, 0);
  sphere(rx * 0.10);
  boxAt(0, -rx * 0.14, 0, 2.5, rx * 0.24, 2.5, trimCol);

  popMatrix();
}

// ============================================================
// SKY CLOUDS (DAY MODE)
// ============================================================

void drawSkyClouds() {
  fill(255, 255, 255, 195);
  noStroke();
  sphereDetail(10);

  float[][] clouds = {
    {-320, -480, -520, 95},
    {-230, -500, -540, 120},
    {-130, -470, -510, 85},
    { 210, -460, -560, 110},
    { 310, -485, -550, 90},
    {  30, -530, -620, 130}
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
// GROUND, MARBLE COURTYARD & SACRED REFLECTING WATER TANK
// ============================================================

void drawGroundAndTank() {
  // Surrounding red earth & garden ground (y = 4)
  boxAt(0, 16, 60, 1350, 24, 1350, EARTH_RED);

  // Temple courtyard platform (top surface at y = 0)
  // Spans around the temple (z = -360..320) and walkways around the front water tank
  boxAt(0, 2, -20, 620, 6, 680, CREAM_DARK);

  // Marble courtyard tile grid in front of stairs (z = 160..310)
  boxAt(0, -0.5, 235, 340, 2, 150, MARBLE_WHITE);
  stroke(MARBLE_GREY);
  strokeWeight(1.2);
  for (float x = -160; x <= 160; x += 32) {
    line(x, -1.8, 162, x, -1.8, 308);
  }
  for (float z = 165; z <= 305; z += 28) {
    line(-168, -1.8, z, 168, -1.8, z);
  }
  noStroke();

  // Sacred Reflecting Tank (Kalyani) in foreground: x = [-210, 210], z = [320, 560]
  // Tank outer concrete apron
  boxAt(-245, 2, 440, 70, 6, 280, CREAM_DARK);
  boxAt( 245, 2, 440, 70, 6, 280, CREAM_DARK);
  boxAt(   0, 2, 585, 560, 6, 50, CREAM_DARK);

  // Tank inner basin walls (pink-terracotta)
  boxAt(   0, 14, 320, 420, 28, 10, PINK_DARK);
  boxAt(   0, 14, 560, 420, 28, 10, PINK_DARK);
  boxAt(-210, 14, 440, 10, 28, 240, PINK_DARK);
  boxAt( 210, 14, 440, 10, 28, 240, PINK_DARK);

  // Water surface at y = 16
  boxAt(0, 22, 440, 412, 12, 232, WATER_DEEP);
  fill(red(WATER_SURF), green(WATER_SURF), blue(WATER_SURF), 215);
  pushMatrix();
  translate(0, 16, 440);
  box(410, 1.5, 230);
  popMatrix();

  // Animated concentric water ripples & subtle temple reflection shimmer
  if (showRipples) {
    noFill();
    for (int r = 0; r < 6; r++) {
      float rad = ((frameNumber * 0.7 + r * 28) % 165);
      float alpha = map(rad, 0, 165, 140, 0);
      stroke(175, 215, 230, alpha);
      strokeWeight(1.5);
      pushMatrix();
      translate(0, 15.0, 435);
      rotateX(HALF_PI);
      ellipse(0, 0, rad * 1.9, rad * 1.15);
      popMatrix();
    }
    // Lotus lily pads in tank corners
    noStroke();
    fill(LEAF_DARK);
    ellipse(-155, 14.8, 24, 18);
    ellipse(-132, 14.8, 18, 14);
    ellipse( 158, 14.8, 22, 16);
  }
}

// ============================================================
// MAIN PRAYER HALL (LOWER HALL + BARREL-VAULT NAVE ROOF)
// ============================================================

void drawMainPrayerHall() {
  // Lower ground hall (y = -110..0, x = -118..118, z = -265..55)
  boxAt(0, -55, -105, 236, 110, 320, PINK_MAIN);

  // Plinth molding & mid-level cornice band
  boxAt(0, -4,   -105, 246, 8, 330, PINK_DARK);
  boxAt(0, -110, -105, 248, 8, 332, CREAM);
  boxAt(0, -115, -105, 242, 4, 326, PINK_LIGHT);

  // Side veranda colonnades & clerestory windows along both flanks (-X and +X)
  for (int side = -1; side <= 1; side += 2) {
    float wx = side * 119;
    // Lower side gallery openings & pillars
    for (float z = -180; z <= -30; z += 30) {
      boxAt(wx, -48, z, 6, 72, 10, PINK_LIGHT);
      boxAt(wx - side * 4, -48, z + 15, 4, 66, 18, WOOD_DARK);
    }
    // Upper hall clerestory arched windows
    for (float z = -175; z <= -25; z += 30) {
      boxAt(wx, -155, z, 6, 48, 16, WOOD_DARK);
      boxAt(wx + side * 1, -155, z, 4, 52, 20, CREAM);
    }
  }

  // Lower ground-floor wooden doorway under the main stairs (right & left flanks)
  boxAt(68, -32, 56, 34, 62, 6, WOOD_DARK);
  boxAt(68, -64, 57, 40, 5, 8, CREAM);
  boxAt(-68, -32, 56, 34, 62, 6, WOOD_DARK);
  boxAt(-68, -64, 57, 40, 5, 8, CREAM);

  // Upper nave walls supporting barrel vault (y = -215..-115, x = -94..94, z = -245..55)
  boxAt(0, -165, -95, 188, 100, 300, PINK_MAIN);

  // Barrel-Vaulted Nave Roof (springing at y = -215, crown at y = -318, z = -245..55)
  drawBarrelVaultNave();
}

void drawBarrelVaultNave() {
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
    float a = PI + i * PI / segs; // PI (left) -> 1.5*PI (top) -> TWO_PI (right)
    float vx = cos(a) * rx;
    float vy = ySpring + sin(a) * ry;
    normal(cos(a), sin(a), 0);
    vertex(vx, vy, zFront);
    vertex(vx, vy, zBack);
  }
  endShape();

  // Cream ridge spine & ornamental cresting along top of barrel vault
  boxAt(0, ySpring - ry - 3, (zFront + zBack) * 0.5, 10, 7, abs(zFront - zBack), CREAM);
  for (float z = zBack + 12; z <= zFront - 12; z += 16) {
    boxAt(0, ySpring - ry - 8, z, 5, 6, 6, CREAM_LIGHT);
  }

  // Transverse roof ribs & lateral dormer buttresses along the barrel vault
  for (float z = -215; z <= 35; z += 35) {
    stroke(CREAM_DARK);
    strokeWeight(3.0);
    noFill();
    beginShape();
    for (int i = 0; i <= segs; i++) {
      float a = PI + i * PI / segs;
      vertex(cos(a) * (rx + 1.5), ySpring + sin(a) * (ry + 1.5), z);
    }
    endShape();
    noStroke();

    // Lateral small Chaitya dormer buttress on each side of the vault
    for (int s = -1; s <= 1; s += 2) {
      boxAt(s * 96, -218, z, 14, 24, 16, PINK_MAIN);
      boxAt(s * 97, -231, z, 16, 5, 18, CREAM);
      drawTempleDome(s * 96, -233, z, 7, 10, 8, PINK_LIGHT, CREAM);
    }
  }
}

// ============================================================
// TALL RIBBED SHIKHARA / REKHA-DEUL SANCTUM TOWER (REAR)
// ============================================================

void drawSanctumShikhara() {
  float cz = -195;
  float yBase = -215;
  float h = 195; // Crown reaches y = -410
  int levels = 18;
  int segs = 28;

  // Square lower base transition (Pabhaga / Jangha)
  boxAt(0, -225, cz, 142, 36, 142, PINK_MAIN);
  boxAt(0, -244, cz, 148, 6, 148, CREAM);

  // Parabolic curvilinear Rekha-Deul spire with vertical Uru-shringa ribs
  noStroke();
  for (int l = 0; l < levels; l++) {
    float t0 = l / (float)levels;
    float t1 = (l + 1) / (float)levels;
    float y0 = yBase - 28 - t0 * (h - 28);
    float y1 = yBase - 28 - t1 * (h - 28);

    // Classic Indian temple curvilinear taper
    float r0 = 68 * pow(1.0 - t0 * 0.76, 0.62);
    float r1 = 68 * pow(1.0 - t1 * 0.76, 0.62);

    beginShape(QUAD_STRIP);
    for (int s = 0; s <= segs; s++) {
      float a = s * TWO_PI / segs;
      // Fluted vertical ribs around circumference
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

  // Necking (Beki), ribbed Amalaka crown, Khapuri & Golden/Cream Kalasha finial
  float yTop = yBase - h;
  cylinderY(0, yTop - 4, cz, 22, 20, 8, 20, CREAM);
  // Amalaka fluted disc
  for (int i = 0; i < 16; i++) {
    float a = i * TWO_PI / 16.0;
    pushMatrix();
    translate(cos(a) * 23, yTop - 11, cz + sin(a) * 23);
    fill(PINK_LIGHT);
    sphereDetail(6);
    sphere(6.5);
    popMatrix();
  }
  cylinderY(0, yTop - 11, cz, 24, 24, 9, 20, CREAM_DARK);
  drawTempleDome(0, yTop - 16, cz, 18, 16, 18, CREAM, CREAM_LIGHT);
  // Tall trident/kalasha spire rod
  boxAt(0, yTop - 42, cz, 2.5, 24, 2.5, CREAM_LIGHT);
}

// ============================================================
// FOUR CORNER SQUARE TOWERS WITH HEMISPHERICAL DOMES
// ============================================================

void drawFourCornerTowers() {
  float[][] corners = {
    {-126,  35}, // Front-Left
    { 126,  35}, // Front-Right
    {-126, -245}, // Rear-Left
    { 126, -245}  // Rear-Right
  };

  for (int i = 0; i < corners.length; i++) {
    float tx = corners[i][0];
    float tz = corners[i][1];

    // Main square tower shaft (y = -238..0)
    boxAt(tx, -119, tz, 64, 238, 64, PINK_MAIN);
    // Plinth & multi-tier cornices
    boxAt(tx, -4,   tz, 72, 8, 72, PINK_DARK);
    boxAt(tx, -110, tz, 72, 7, 72, CREAM);
    boxAt(tx, -182, tz, 70, 6, 70, CREAM_DARK);
    boxAt(tx, -238, tz, 74, 8, 74, CREAM);

    // Upper tower perforated jali window niches on outward faces
    boxAt(tx, -206, tz + (tz > 0 ? 33 : -33), 24, 24, 3, CREAM_DARK);
    boxAt(tx + (tx > 0 ? 33 : -33), -206, tz, 3, 24, 24, CREAM_DARK);

    // Ornate corner eave scrolls (Kodungai) at roof corners
    for (int cx = -1; cx <= 1; cx += 2) {
      for (int cz = -1; cz <= 1; cz += 2) {
        boxAt(tx + cx * 33, -232, tz + cz * 33, 12, 14, 12, CREAM_LIGHT);
      }
    }

    // Hemispherical temple dome with finial
    drawTempleDome(tx, -242, tz, 31, 38, 31, PINK_LIGHT, CREAM);
  }

  // Black marble commemorative inscription plaque on front-left tower base
  boxAt(-126, -68, 68, 44, 30, 3, BLACK_PLAQUE);
  boxAt(-126, -68, 67.5, 48, 34, 2, CREAM_DARK);

  // Small arched shrine windows & circular medallions flanking the front facade
  for (int side = -1; side <= 1; side += 2) {
    float wx = side * 80;
    boxAt(wx, -152, 57, 18, 26, 4, WOOD_DARK);
    boxAt(wx, -152, 58, 24, 32, 3, CREAM);
    // Triangular gable hood over small window
    boxAt(wx, -171, 59, 26, 6, 5, CREAM_LIGHT);

    // Cream circular medallion on front corner tower lower shaft
    pushMatrix();
    translate(side * 126, -138, 68);
    fill(CREAM);
    ellipse(0, 0, 16, 16);
    popMatrix();
  }
}

// ============================================================
// GRAND CHAITYA ARCH GABLE & CENTRAL EMBLEM ROSE WINDOW
// ============================================================

void drawChaityaFacadeAndEmblem() {
  float zFace = 57;
  float ySpring = -215;
  float rx = 96;
  float ry = 104;

  // Solid recessed tympanum wall inside the Chaitya arch
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

  // Outer thick cream Chaitya arch molding (Kudu)
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
    boxAt(0, 0, 0, 14, 14, 14, CREAM_LIGHT);
    boxAt(0, 7, 1, 14, 4, 15, PINK_LIGHT);
    popMatrix();
  }

  // Ornate Kirtimukha / Lotus Crown Crest at apex of Chaitya arch (y = -328)
  float apexY = ySpring - ry - 14;
  boxAt(0, apexY, zFace + 6, 18, 24, 12, CREAM_LIGHT);
  pushMatrix();
  translate(0, apexY - 8, zFace + 8);
  fill(CREAM);
  sphereDetail(10);
  sphere(10);
  popMatrix();

  // Ornate Makara flared base scrolls at left & right springings of the Chaitya arch
  for (int side = -1; side <= 1; side += 2) {
    boxAt(side * (rx + 4), ySpring + 4, zFace + 6, 22, 32, 16, CREAM_LIGHT);
    boxAt(side * (rx + 8), ySpring + 12, zFace + 8, 16, 16, 14, CREAM_DARK);
  }

  // Cascading vertical stepped cream ornament drops inside the Chaitya tympanum
  for (int k = -4; k <= 4; k++) {
    float dx = k * 16.5;
    float topArchY = ySpring - ry * sqrt(max(0.05, 1.0 - (dx / (rx - 8)) * (dx / (rx - 8)))) + 10;
    float botDropY = (abs(k) <= 1) ? -266 : -232;
    if (botDropY > topArchY + 6) {
      float hDrop = botDropY - topArchY;
      boxAt(dx, topArchY + hDrop * 0.5, zFace + 3, 5, hDrop, 5, CREAM);
      // Little lotus beads along each drop
      for (float by = topArchY + 6; by <= botDropY; by += 11) {
        boxAt(dx, by, zFace + 5, 8, 5, 6, CREAM_LIGHT);
      }
    }
  }

  // Central Circular Stained-Glass Ramakrishna Order Emblem Window (y = -236)
  float ey = -236;
  float ez = zFace + 6;
  pushMatrix();
  translate(0, ey, ez);

  // Outer carved cream wreath ring
  noStroke();
  fill(CREAM_LIGHT);
  ellipse(0, 0, 56, 56);
  fill(PINK_DEEP);
  ellipse(0, 0, 44, 44);

  // 16 radiating white/cream wheel spokes
  stroke(CREAM_LIGHT);
  strokeWeight(2.0);
  for (int s = 0; s < 16; s++) {
    float a = s * TWO_PI / 16.0;
    line(cos(a) * 11, sin(a) * 11, cos(a) * 21, sin(a) * 21);
  }
  noStroke();

  // Inner blue waters, rising sun & white swan (emblem motif)
  fill(eveningMode ? 0xFF56B4F5 : EMBLEM_BLUE);
  ellipse(0, 0, 24, 24);
  fill(EMBLEM_SUN);
  arc(0, 2, 16, 16, PI, TWO_PI);
  fill(WHITE);
  ellipse(0, 3, 10, 5);

  popMatrix();
}

// ============================================================
// RECESSED SANCTUM ENTRANCE PORCH, LION PILLARS & MARBLE STAIRS
// ============================================================

void drawEntrancePorchAndStairs() {
  // Upper sanctum landing deck (y = -110, z = 55..105)
  boxAt(0, -55, 80, 74, 110, 50, PINK_MAIN);
  boxAt(0, -110, 80, 78, 6, 54, MARBLE_WHITE);

  // Deep sanctum doorway opening
  boxAt(0, -155, 58, 46, 86, 8, 0xFF1A0E0C);
  // Inner glowing sanctum shrine lamp light
  if (eveningMode) {
    boxAt(0, -145, 54, 18, 24, 4, 0xFFFFD275);
  }
  boxAt(0, -155, 60, 54, 92, 4, CREAM);

  // Ornate projecting porch canopy (Kapota) above entrance (y = -202, z = 84)
  boxAt(0, -202, 84, 84, 10, 54, PINK_LIGHT);
  boxAt(0, -208, 84, 88, 6, 58, CREAM_LIGHT);
  boxAt(0, -196, 84, 78, 5, 48, CREAM_DARK);
  // Miniature kudu arches along porch cornice
  for (float kx = -32; kx <= 32; kx += 16) {
    boxAt(kx, -213, 111, 8, 7, 5, CREAM_LIGHT);
  }

  // Twin Porch Columns resting on Sculpted Sitting Lions (Yali / Simha)
  for (int side = -1; side <= 1; side += 2) {
    float px = side * 28;
    float pz = 100;
    // Sculpted lion pedestal (y = -134..-110)
    boxAt(px, -122, pz, 15, 24, 18, CREAM_LIGHT);
    boxAt(px, -128, pz + 6, 12, 12, 10, CREAM); // Lion head/mane
    // Cylindrical column shaft & lotus capital (y = -196..-134)
    cylinderY(px, -165, pz, 6.5, 5.5, 62, 14, PINK_LIGHT);
    cylinderY(px, -192, pz, 9.5, 11.0, 8, 14, CREAM_LIGHT);
  }

  // Grand Ceremonial Staircase descending from (y = -110, z = 105) to (y = 0, z = 255)
  int numSteps = 22;
  float zTop = 105;
  float zBot = 255;
  float yTop = -110;
  float yBot = 0;

  // Solid structural wedge beneath stairs
  noStroke();
  fill(PINK_MAIN);
  beginShape(QUADS);
  normal(-1, 0, 0);
  vertex(-36, yTop, zTop);
  vertex(-36, yBot, zBot);
  vertex(-36, yBot, zTop);
  vertex(-36, yTop, zTop);

  normal(1, 0, 0);
  vertex(36, yTop, zTop);
  vertex(36, yTop, zTop);
  vertex(36, yBot, zTop);
  vertex(36, yBot, zBot);
  endShape();

  // Individual veined marble treads & risers
  float stepH = (yBot - yTop) / numSteps;
  float stepD = (zBot - zTop) / numSteps;
  for (int i = 0; i < numSteps; i++) {
    float sy = yTop + (i + 0.5) * stepH;
    float sz = zTop + (i + 0.5) * stepD;
    // Fill down to ground
    boxAt(0, (sy + yBot) * 0.5, sz, 70, yBot - sy, stepD + 1, PINK_DARK);
    // Marble tread with alternating natural grey veining
    int treadCol = (i % 3 == 1) ? 0xFFD6D8DE : MARBLE_WHITE;
    boxAt(0, sy - stepH * 0.45, sz, 72, 3.2, stepD + 2.5, treadCol);
  }

  // Sloping stepped balustrades & turned cream balusters on both sides of stairs
  for (int side = -1; side <= 1; side += 2) {
    float bx = side * 38;
    for (int i = 0; i <= 14; i++) {
      float t = i / 14.0;
      float sy = lerp(yTop, yBot, t);
      float sz = lerp(zTop, zBot, t);
      // Base stringer
      boxAt(bx, sy - 4, sz, 8, 10, 14, PINK_MAIN);
      // Turned cream baluster post
      cylinderY(bx, sy - 16, sz, 2.5, 2.0, 16, 8, CREAM_LIGHT);
      // Top sloping handrail
      boxAt(bx, sy - 26, sz, 10, 5, 14, PINK_LIGHT);
    }
    // Bottom newel post & finial at foot of stairs
    boxAt(bx, -18, zBot + 4, 14, 36, 14, PINK_MAIN);
    drawTempleDome(bx, -36, zBot + 4, 7, 9, 7, PINK_LIGHT, CREAM);
  }
}

// ============================================================
// TWO ORNATE DRAVIDIAN VIMANA FLANKING SHRINES
// ============================================================

void drawFlankingDravidianShrines() {
  // Positioned symmetrically at the left and right base of the grand stairs
  drawDravidianShrine(-88, 152);
  drawDravidianShrine( 88, 152);
}

void drawDravidianShrine(float sx, float sz) {
  pushMatrix();
  translate(sx, 0, sz);

  // 1. Stepped Adhishthana base plinth (y = -24..0)
  boxAt(0, -6,  0, 68, 12, 68, PINK_DARK);
  boxAt(0, -18, 0, 60, 12, 60, PINK_MAIN);
  boxAt(0, -25, 0, 64, 4,  64, CREAM);

  // 2. Sanctum wall & front arched doorway (y = -76..-26)
  boxAt(0, -51, 0, 52, 50, 52, PINK_MAIN);
  // Front shrine entrance & carved cream torana arch
  boxAt(0, -48, 26, 22, 38, 4, WOOD_DARK);
  boxAt(0, -68, 27, 28, 7, 6, CREAM_LIGHT);
  for (int p = -1; p <= 1; p += 2) {
    boxAt(p * 13, -48, 27, 4, 38, 4, CREAM);
  }

  // 3. Projecting cornice (Kapota) with cream kudus (y = -80)
  boxAt(0, -79, 0, 62, 7, 62, CREAM_LIGHT);

  // 4. First Dravidian Tala (tier) with miniature Kuta & Sala roof shrines (y = -102..-82)
  boxAt(0, -92, 0, 44, 20, 44, PINK_MAIN);
  boxAt(0, -103, 0, 48, 5, 48, CREAM);
  // Miniature corner shrine turrets around tier 1
  for (int cx = -1; cx <= 1; cx += 2) {
    for (int cz = -1; cz <= 1; cz += 2) {
      boxAt(cx * 21, -94, cz * 21, 9, 14, 9, CREAM_LIGHT);
    }
  }
  boxAt(0, -94, 23, 14, 14, 7, CREAM_LIGHT);

  // 5. Second Tala & Griva necking (y = -118..-105)
  boxAt(0, -111, 0, 30, 13, 30, PINK_LIGHT);
  boxAt(0, -119, 0, 34, 4,  34, CREAM);

  // 6. Octagonal/Hemispherical Dravidian Shikhara Cupola & Stupi Finial
  drawTempleDome(0, -121, 0, 17, 22, 17, PINK_LIGHT, CREAM_LIGHT);

  popMatrix();
}

// ============================================================
// BALUSTRADE PARAPET WALLS, CHHATRI KIOSKS & VIEWING SHRINE
// ============================================================

void drawBalustradeCourtyardAndKiosks() {
  // Courtyard front parapet wall separating marble terrace from water tank (z = 315)
  // Left & Right segments leaving central opening for the tank viewing pavilion
  drawBalustradeWall(-225, 315, -36, 315, 14);
  drawBalustradeWall(  36, 315, 225, 315, 14);

  // Outer wing balustrade walls extending left & right
  drawBalustradeWall(-420, 270, -225, 270, 12);
  drawBalustradeWall( 225, 270,  420, 270, 12);

  // Perimeter balustrade walls enclosing the sacred water tank (z = 315..565)
  drawBalustradeWall(-225, 315, -225, 565, 14);
  drawBalustradeWall( 225, 315,  225, 565, 14);
  drawBalustradeWall(-225, 565,  225, 565, 24);

  // Central Tank-Edge Shrine / Viewing Mandapa (x = 0, z = 315)
  boxAt(0, -26, 315, 64, 52, 46, PINK_MAIN);
  boxAt(0, -24, 317, 32, 34, 48, 0xFF1F1614); // Open shrine niche
  boxAt(0, -54, 315, 72, 7, 54, CREAM_LIGHT);
  for (float kx = -22; kx <= 22; kx += 14.5) {
    boxAt(kx, -61, 338, 7, 8, 5, CREAM_LIGHT);
  }

  // Ornate Corner Chhatri Kiosk Towers along the parapet perimeter
  float[][] kiosks = {
    {-225, 315}, { 225, 315},
    {-225, 565}, { 225, 565},
    {-155, 315}, { 155, 315},
    {-330, 270}, { 330, 270},
    {-420, 270}, { 420, 270}
  };
  for (int i = 0; i < kiosks.length; i++) {
    drawParapetKiosk(kiosks[i][0], kiosks[i][1]);
  }
}

void drawBalustradeWall(float x1, float z1, float x2, float z2, int count) {
  float dx = x2 - x1;
  float dz = z2 - z1;
  float len = sqrt(dx * dx + dz * dz);
  float ang = -atan2(dz, dx);
  float cx = (x1 + x2) * 0.5;
  float cz = (z1 + z2) * 0.5;

  pushMatrix();
  translate(cx, 0, cz);
  rotateY(ang);

  // Lower solid terracotta dado wall (y = -18..0)
  boxAt(0, -9, 0, len, 18, 10, PINK_MAIN);
  boxAt(0, -19, 0, len + 2, 4, 13, CREAM);

  // Top terracotta coping rail (y = -42)
  boxAt(0, -42, 0, len + 2, 6, 13, PINK_LIGHT);
  boxAt(0, -39, 0, len, 3, 11, CREAM_DARK);

  // Turned cream balusters along the span
  for (int i = 1; i < count; i++) {
    float bx = lerp(-len * 0.5, len * 0.5, i / (float)count);
    cylinderY(bx, -29, 0, 3.0, 2.4, 18, 8, CREAM_LIGHT);
    boxAt(bx, -29, 0, 7.5, 4, 7.5, CREAM);
  }

  popMatrix();
}

void drawParapetKiosk(float kx, float kz) {
  pushMatrix();
  translate(kx, 0, kz);
  boxAt(0, -24, 0, 30, 48, 30, PINK_MAIN);
  boxAt(0, -24, 0, 12, 22, 32, WOOD_DARK);
  boxAt(0, -50, 0, 36, 6, 36, CREAM_LIGHT);
  drawTempleDome(0, -53, 0, 15, 18, 15, PINK_LIGHT, CREAM);
  popMatrix();
}

// ============================================================
// LUSH TROPICAL TREES, FAN PALM & POTTED FOREGROUND PLANTS
// ============================================================

void drawSurroundingTreesAndPots() {
  // Dense canopy trees framing left and right of temple (matching photos)
  drawCanopyTree(-250, 0,  -40, 290, 110);
  drawCanopyTree(-270, 0, -190, 320, 125);
  drawCanopyTree(-210, 0, -310, 300, 115);
  drawCanopyTree( 245, 0, -160, 310, 120);
  drawCanopyTree( 265, 0, -290, 330, 130);

  // Tall Fan Palm (Borassus) on right side
  drawFanPalm(235, 0, 20, 285);

  // Row of potted ornamental plants in front of tank wall
  for (int i = -3; i <= 3; i++) {
    float px = i * 56;
    float pz = 582;
    cylinderY(px, -8, pz, 8, 11, 16, 10, 0xFFB89638);
    pushMatrix();
    translate(px, -24, pz);
    noStroke();
    fill((i % 2 == 0) ? LEAF_MID : LEAF_LIGHT);
    sphereDetail(6);
    sphere(14);
    popMatrix();
  }
}

void drawCanopyTree(float tx, float ty, float tz, float h, float rad) {
  stroke(TRUNK_BROWN);
  strokeWeight(14);
  line(tx, ty, tz, tx, ty - h * 0.65, tz);
  noStroke();

  pushMatrix();
  translate(tx, ty - h * 0.72, tz);
  sphereDetail(7);
  fill(LEAF_DARK);
  sphere(rad * 0.85);
  translate(-rad * 0.42, 18, rad * 0.25);
  fill(LEAF_MID);
  sphere(rad * 0.68);
  translate(rad * 0.85, -12, -rad * 0.35);
  fill(LEAF_LIGHT);
  sphere(rad * 0.62);
  popMatrix();
}

void drawFanPalm(float px, float py, float pz, float h) {
  float topY = py - h;
  stroke(TRUNK_BROWN);
  strokeWeight(10);
  line(px, py, pz, px + 8, topY, pz);
  noStroke();

  pushMatrix();
  translate(px + 8, topY, pz);
  for (int f = 0; f < 12; f++) {
    float yaw = f * TWO_PI / 12.0;
    float pitch = (f % 2 == 0) ? 0.35 : -0.25;
    pushMatrix();
    rotateY(yaw);
    rotateZ(pitch);
    stroke(LEAF_MID);
    strokeWeight(4);
    line(0, 0, 0, 52, 10, 0);
    // Fan palm fronds radiating from petiole tip
    translate(52, 10, 0);
    strokeWeight(2.2);
    for (int r = -5; r <= 5; r++) {
      float fa = r * 0.24;
      stroke((r % 2 == 0) ? LEAF_DARK : LEAF_LIGHT);
      line(0, 0, 0, cos(fa) * 38, sin(fa) * 28 + 12, sin(fa) * 24);
    }
    popMatrix();
  }
  noStroke();
  popMatrix();
}

// ============================================================
// INTERACTIVE TOUCH HUD & CAMERA CONTROLS
// ============================================================

float panelW() {
  return min(296, width * 0.46);
}

float panelH() {
  return min(215, height - 24);
}

void drawMiniHudButton() {
  hint(DISABLE_DEPTH_TEST);
  camera();
  noLights();

  fill(34, 22, 26, 220);
  stroke(CREAM);
  strokeWeight(1.4);
  rect(12, 12, 64, 28, 8);

  noStroke();
  fill(CREAM_LIGHT);
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
  fill(34, 22, 26, 232);
  rect(x, y, w, h, 12);

  stroke(CREAM);
  strokeWeight(1.5);
  noFill();
  rect(x, y, w, h, 12);
  noStroke();

  fill(CREAM_LIGHT);
  textAlign(LEFT, TOP);
  textSize(12.5);
  text("RAMAKRISHNA MATH 3D", x + 12, y + 10);

  fill(PINK_LIGHT);
  textSize(8.8);
  text("UNIVERSAL TEMPLE | APDE P3D", x + 12, y + 26);

  // Hide HUD button
  fill(68, 42, 46);
  stroke(CREAM_DARK);
  strokeWeight(1);
  rect(x + w - 48, y + 10, 36, 18, 5);
  noStroke();
  fill(WHITE);
  textAlign(CENTER, CENTER);
  textSize(8.5);
  text("HIDE", x + w - 30, y + 19);

  float bw = (w - 30) / 2.0;
  float bh = 26;

  // Row 1: Front Tank View | Elevated Aerial View
  drawBtn("TANK FRONT VIEW", x + 12, y + 46, bw, bh, 0xFF8C4A4B);
  drawBtn("AERIAL ANGLE",    x + 18 + bw, y + 46, bw, bh, 0xFF8C4A4B);

  // Row 2: Shikhara Rear View | Reset Camera
  drawBtn("SHIKHARA VIEW",   x + 12, y + 78, bw, bh, 0xFF6E3B3E);
  drawBtn("RESET CAMERA",    x + 18 + bw, y + 78, bw, bh, 0xFF54363A);

  // Row 3: Day / Evening Lighting | Tank Ripples Toggle
  drawBtn(eveningMode ? "DAY SKY" : "EVENING GLOW", x + 12, y + 110, bw, bh, eveningMode ? 0xFFB87828 : 0xFF395878);
  drawBtn(showRipples ? "RIPPLES: ON" : "RIPPLES: OFF", x + 18 + bw, y + 110, bw, bh, 0xFF2B586C);

  // Zoom controls
  drawBtn("ZOOM +", x + 12, y + 142, bw, bh, 0xFF4B3A40);
  drawBtn("ZOOM -", x + 18 + bw, y + 142, bw, bh, 0xFF4B3A40);

  fill(CREAM_DARK);
  textAlign(LEFT, TOP);
  textSize(8.2);
  text("Drag screen to orbit 360 | +/-: Zoom", x + 12, y + 178);
  text("Keys: N=Evening, R=Reset, H=Toggle HUD", x + 12, y + 191);

  hint(ENABLE_DEPTH_TEST);
}

void drawBtn(String label, float bx, float by, float bw, float bh, int col) {
  fill(col);
  stroke(CREAM_DARK);
  strokeWeight(1.1);
  rect(bx, by, bw, bh, 6);
  noStroke();
  fill(CREAM_LIGHT);
  textAlign(CENTER, CENTER);
  textSize(9.0);
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

  // Hide button
  if (mouseX >= x + w - 48 && mouseX <= x + w - 12 &&
      mouseY >= y + 10 && mouseY <= y + 28) {
    showHUD = false;
    return;
  }

  float bw = (w - 30) / 2.0;
  float bh = 26;

  // Row 1
  if (mouseY >= y + 46 && mouseY <= y + 46 + bh) {
    if (mouseX >= x + 12 && mouseX <= x + 12 + bw) {
      // Tank Front View
      targetYaw = 0.0;
      targetPitch = 0.14;
      targetZoom = 0.90;
      return;
    }
    if (mouseX >= x + 18 + bw && mouseX <= x + 18 + bw * 2) {
      // Elevated Aerial View
      targetYaw = -0.52;
      targetPitch = 0.44;
      targetZoom = 1.05;
      return;
    }
  }

  // Row 2
  if (mouseY >= y + 78 && mouseY <= y + 78 + bh) {
    if (mouseX >= x + 12 && mouseX <= x + 12 + bw) {
      // Shikhara Side/Rear View
      targetYaw = 2.35;
      targetPitch = 0.24;
      targetZoom = 0.96;
      return;
    }
    if (mouseX >= x + 18 + bw && mouseX <= x + 18 + bw * 2) {
      resetCamera();
      return;
    }
  }

  // Row 3
  if (mouseY >= y + 110 && mouseY <= y + 110 + bh) {
    if (mouseX >= x + 12 && mouseX <= x + 12 + bw) {
      eveningMode = !eveningMode;
      return;
    }
    if (mouseX >= x + 18 + bw && mouseX <= x + 18 + bw * 2) {
      showRipples = !showRipples;
      return;
    }
  }

  // Row 4: Zoom
  if (mouseY >= y + 142 && mouseY <= y + 142 + bh) {
    if (mouseX >= x + 12 && mouseX <= x + 12 + bw) {
      targetZoom = min(1.85, targetZoom + 0.12);
      return;
    }
    if (mouseX >= x + 18 + bw && mouseX <= x + 18 + bw * 2) {
      targetZoom = max(0.50, targetZoom - 0.12);
      return;
    }
  }
}

void mouseDragged() {
  if (!showHUD || mouseX > panelW() + 20 || mouseY > panelH() + 20) {
    targetYaw += (mouseX - previousMouseX) * 0.006;
    targetPitch = constrain(
      targetPitch + (mouseY - previousMouseY) * 0.004,
      -0.05,
      0.65
    );
    cameraYaw = targetYaw;
    cameraPitch = targetPitch;
  }
  previousMouseX = mouseX;
  previousMouseY = mouseY;
}

void resetCamera() {
  targetYaw   = -0.34;
  targetPitch =  0.22;
  targetZoom  =  0.92;
}

void mouseWheel(processing.event.MouseEvent event) {
  // Intentionally empty for APDE Android compatibility
}

void keyPressed() {
  if (key == 'h' || key == 'H') showHUD = !showHUD;
  if (key == 'r' || key == 'R') resetCamera();
  if (key == 'n' || key == 'N') eveningMode = !eveningMode;
  if (key == '+' || key == '=') targetZoom = min(1.85, targetZoom + 0.12);
  if (key == '-')                targetZoom = max(0.50, targetZoom - 0.12);
}

// ============================================================
// END OF SRI RAMAKRISHNA MATH 3D APDE SKETCH
// ============================================================
