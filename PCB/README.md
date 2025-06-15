# PCB Directory

This directory contains files related to the printed circuit board (PCB) design for the Falcon Flight project.

## Files

* **Assembly_Layout.pdf**: Single PDF document showing each of the following images.

* **Top Assembly Drawings.svg**: SVG file of the top assembly drawings. Includes silkscreen.
  <img src="/PCB/Top_Assembly_Drawings.svg" alt="SVG file of the top assembly drawings. Includes silkscreen." width="50%" ALIGN="right" HSPACE="20" VSPACE="20"/>

* **Bottom Assembly Drawings.svg**: SVG file of the bottom assembly drawings. Includes silkscreen.
  <img src="/PCB/Bottom_Assembly_Drawings.svg" alt="SVG file of the bottom assembly drawings. Includes silkscreen." width="50%" ALIGN="right" HSPACE="20" VSPACE="20"/>

* **Top Layer.svg**: SVG file of the top copper layer. Silkscreen omitted.
  <img src="/PCB/Top_Layer.svg" alt="SVG file of the top copper layer. Silkscreen omitted." width="50%" ALIGN="right" HSPACE="20" VSPACE="20"/>
  
* **Inner Layer 1.svg**: SVG file of the first inner copper layer. This layer consists entirely of a ground pour to reduce EMI. As an auxillary effect, it shields sensitive data traces on the 2nd and bottom copper layers from the MT3608's switching noise.
  <img src="/PCB/Inner_Layer 1.svg" alt="SVG file of the first inner copper layer." width="50%" ALIGN="right" HSPACE="20" VSPACE="20"/>

* **Inner Layer 2.svg**: SVG file of the second inner copper layer. Contains some auxillary data traces that wouldn't fit on the top and bottom layers, and split power zones for the Raspberry Pi and motor drivers.
  <img src="/PCB/Inner Layer 2.svg" alt="SVG file of the second inner copper layer." width="50%" ALIGN="right" HSPACE="20" VSPACE="20"/>

* **Bottom Layer.svg**: SVG file of the bottom copper layer. Silkscreen omitted.
  <img src="/PCB/Bottom_Layer.svg" alt="SVG file of the bottom copper layer. Silkscreen omitted." width="50%" ALIGN="right" HSPACE="20" VSPACE="20"/>

* **Drill Drawing.svg**: SVG file of the drill drawing. Includes all 4 layers, silkscreen omitted.
  <img src="/PCB/Drill_Drawing.svg" alt="SVG file of the drill drawing. Includes all 4 layers, silkscreen omitted." width="50%" ALIGN="right" HSPACE="20" VSPACE="20"/>

* **FFB6.1_EasyEDA_Pro_Document.epro**: EasyEDA Pro project file for the PCB design.
* **FFB6.1_PCB_Fab_Gerber.zip**: ZIP archive containing the Gerber files for PCB fabrication.
* **FFB6.1_PCB_Pick_and_Place.xlsx**: Pick and place archive for PCB fabrication. 


**Note:** The BOM can be found [here](/docs/FFB6.1_BOM.xlsx) to complete PCB ordering.

