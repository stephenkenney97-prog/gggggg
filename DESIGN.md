---
name: Iron Beam Simulation
colors:
  background: "#000000"
  primary: "#00FF88"
  secondary: "#00FFFF"
  metric: "#FFFF00"
  ready: "#00FF00"
  active: "#FF4444"
typography:
  family: "'Courier New', monospace"
  body: "12px"
  section: "14px"
spacing:
  viewportInset: "20px"
  panelPadding: "15px"
radii:
  panel: "12px"
  control: "6px"
---

The interface feels like an illuminated instrument console over a full-screen
3D scene. Dark translucent panels and compact numerical readouts frame the
scene; green and cyan provide the visual structure. This records the current
visual conventions in `index.html` and guides future changes.

## Scene and panels

Keep the canvas as the full viewport background. Place system status in the
upper left, atmosphere in the upper right, engagement metrics in the lower left,
and mission controls in the lower right. Current desktop panel widths are
350px, 280px, 400px, and 300px respectively.

Panels use dark navy gradients, green-to-cyan borders, a subtle green glow, and
background blur. Use the frontmatter values for insets, padding, and radii when
extending the interface. At narrow widths, adapt the panel layout so controls
remain reachable and text stays readable.

## Type and status

Use monospace text, uppercase cyan section titles, paired labels and values,
and bold yellow numerical metrics. Green indicates ready, red indicates active,
and cyan indicates manual mode. Pair status colors with visible text labels.

## Controls and interaction

Buttons use green gradients and a cyan active state. Preserve their short text
labels and familiar grouping. Keep interactive controls clickable while allowing
pointer input over the scene outside those controls. Preserve camera interaction
when adding overlays. New controls need visible keyboard focus and text labels;
do not rely solely on color, icons, or animation to communicate state.

## Reviewing changes

Check the scene with all panels visible, with the HUD hidden, and after reset.
Check long values, viewport resizing, and a narrow viewport. Confirm that panels
do not obscure essential controls or prevent camera use.
