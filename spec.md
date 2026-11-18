# Beam Designer

## Purpose
A beam design tool for engineers and students. The user sets up a beam,
places loads on it, and picks a steel or lumber section. The app shows the
shear, moment, and deflection diagrams and checks whether the beam is
strong and stiff enough, updating instantly as anything changes.

## Users
A civil engineering student checking a homework beam, or an engineer
sizing a floor beam or deck joist.

## Inputs and interactions
- Support type: Simply supported or Cantilever (fixed at the left end).
  The default is Simply supported. Use a selector control.
- Span: 4 to 40 ft, default 20 ft, set with a slider and a number box
  that stay in sync. The unit is ft.
- Loads, listed in a panel where each can be edited or removed:
  - Point loads: signed load value from any finite value in kips, with downward
    positive and upward loads allowed, and position from 0 to the span in ft.
    The default magnitude is 10 kips. A new point load starts at midspan
    (at the free end of a cantilever) and its position snaps to 0.25 ft.
    Use number boxes for the magnitude and position.
  - Uniform loads: downward magnitude from 0 to any finite value in kips/ft,
    and start and end positions from 0 to the span in ft. The default
    magnitude is 50 kips/ft, with a default range from 0 ft to the full span.
    Use number boxes for the magnitude, start position, and end position.
    The start position must be before the end position.
  - There is no limit on the number of point loads or uniform loads.
    Use "Add point load" and "Add uniform load" buttons.
  - Point loads can also be dragged along the beam in the drawing,
    snapping to 0.25 ft.
  - Loads that extend past the end of the beam are cut off at the end.
- Material: Steel or Douglas Fir-Larch No. 2, with Steel selected by default.
  Use a selector control.
- Section: one of the sections in the table for the selected material, with
  W12x26 selected by default. Use a selector control.
- Deflection limit: L/240 or L/360, with L/360 selected by default. This is
  a dimensionless span ratio, and the control is a selector.
- Waste factor: 0% to 15%, default 0%, controlled by a slider. Display the
  current value as a percentage.
- Self-weight is ignored and is not an input.

Material and section data come from these tables:

  Steel W-shapes (E = 29,000 ksi, allowable bending stress Fb = 30 ksi):

  | Section | Ix (in^4) | Sx (in^3) | Weight (lb/ft) |
  |---------|-----------|-----------|----------------|
  | W8x10   | 30.8      | 7.81      | 10 |
  | W10x12  | 53.8      | 10.9      | 12 |
  | W12x26  | 204       | 33.4      | 26 |
  | W14x22  | 199       | 29.0      | 22 |
  | W16x26  | 301       | 38.4      | 26 |
  | W18x35  | 510       | 57.6      | 35 |
  | W21x44  | 843       | 81.6      | 44 |
  | W24x55  | 1350      | 114       | 55 |

  Douglas Fir-Larch No. 2 lumber (E = 1,600 ksi, Fb = 0.900 ksi):

  | Section | Ix (in^4) | Sx (in^3) |
  |---------|-----------|-----------|
  | 2x8     | 47.63     | 13.14     |
  | 2x10    | 98.93     | 21.39     |
  | 2x12    | 177.98    | 31.64     |


## Input validation and general behavior
- Inputs must be validated as they are edited. Invalid values must be clearly identified, and calculations and recommendations must not use invalid input.
- Span, load magnitudes, and load positions must stay within the allowed ranges. A uniform load must have a start position before its end position.
- Loads that extend past the end of the beam are cut off at the end; loads with no remaining length on the beam are not included in calculations.
- The app must handle zero loads and any valid combination of point and uniform loads without errors.
- If no section in the selected material passes both checks, the app must say so instead of recommending a section.
- Calculated values must use consistent engineering units internally and display a consistent, readable precision. Values must be rounded only for presentation, not before checks are performed.
- PASS or FAIL must not be communicated by color alone. Inputs, controls, diagrams, and check cards must be usable with a keyboard and have labels that identify their purpose and units.
- The app must run in a current desktop or mobile web browser and must not require a network connection after it has loaded.

## Calculations
- Reactions from statics.
- Shear V(x) and moment M(x) along the beam, at 1 ft spacing or finer,
  plus exactly at every load point and load start/end.
- Deflection y(x) by integrating M/EI twice with the boundary conditions
  for the support type (y = 0 at both supports; or y = 0 and slope = 0 at
  the fixed end).
- Bending stress fb = |M max| x 12 / Sx (ksi). Bending check passes if
  fb <= Fb.
- Deflection check passes if |y max| <= L/240 or L/360 (L in inches).
- Lightest passing section: of the sections in the chosen material, the
  lightest steel section that passes both checks (if two weigh the same,
  the one with the larger Ix), or the first lumber size in the table
  that passes.
- Material quantity: base beam length is L in ft, and the waste-adjusted
  quantity is L x (1 + waste factor / 100). Waste changes quantity only; it
  does not change structural loads, section properties, bending demand, or
  deflection.
- Units: kips, ft, and kip-ft on screen; inches for deflection.

## Outputs
- A beam drawing with the supports, load arrows (point loads as single
  arrows, uniform loads as a row of small arrows), and the reactions.
- Three diagrams stacked under the beam and lined up with it on the same
  x scale: shear (kips), moment (kip-ft), and deflected shape (in). Each
  diagram is filled and shows its maximum value labeled at its location.
- Moving the mouse over any diagram shows a vertical line across all three
  with the values of V, M, and y at that x.
- Two check cards: Bending and Deflection. Each shows the actual value, the
  allowable value, a bar for the ratio (green under 70%, amber 70-100%,
  red over 100%), and PASS or FAIL.
- A material quantity card showing the base beam length, waste factor, and
  waste-adjusted required length in ft.
- A line suggesting the lightest passing section, with a button to use it.
- A reinforcement-required warning when any implemented bending or deflection
  check fails. The warning must not rely on color alone.
- Everything updates immediately whenever any input changes.

## Validation
1. Simply supported, 20 ft, W12x26, uniform 1 kip/ft over the full span:
   reactions 10 k and 10 k; max shear 10 k; max moment 50.0 kip-ft at
   10 ft; fb = 17.96 ksi; max deflection 0.609 in at 10 ft; L/360 =
   0.667 in; both checks pass.
2. Simply supported, 20 ft, W12x26, point load 10 k at 5 ft: reactions
   7.5 k (left) and 2.5 k (right); max moment 37.5 kip-ft at 5 ft;
   fb = 13.47 ksi; max deflection 0.340 in at 8.82 ft.
3. Cantilever, 10 ft, W12x26, point load 5 k at the free end: reaction
   5 k and fixed-end moment 50 kip-ft; max deflection 0.487 in at the free
   end (PL^3/3EI); L/360 = 0.333 in, so deflection fails.
4. Simply supported 12 ft 2x10 joist, uniform 0.05 kip/ft: max moment
   0.90 kip-ft; fb = 0.505 ksi; max deflection 0.147 in; both pass.

## Look and feel
- Opens with an example already loaded (20 ft simply supported W12x26
  with a 1 kip/ft uniform load over the full span), so it is useful the
  moment it opens.
- Clean and professional, like a commercial engineering web app: light
  background, white cards with soft shadows, one accent color, a modern
  system font, generous spacing. No clutter.
- Inputs in a panel on the left; drawing, diagrams, and checks on the
  right. On a phone, the panels stack.
- Every input has a short label with units. Hovering over a check card
  explains the formula used.

## Out of scope
- Self-weight, shear capacity, lateral-torsional buckling, load combinations,
  and continuous beams.
- Saving designs.