# 🖨️ Printing an A1 Poster on 4 Sheets of A3

Author: Elle


This guide details how to split an A1 poster into four separate A3 PDF sheets for printing on University of Leeds printers.

---

## 1. Initial Setup in PowerPoint and PDF Creation

1.  Ensure your poster is correctly formatted as an **A1** size document in **PowerPoint**.
2.  Go to the print dialogue.
3.  Select the **PDF-XChange 5.0 for ABBYY** printer option as shown in the image. 
    * *Note: This acts as a virtual printer to convert the document.*
![selecting PDF print](assets/printing_1.png)

---

## 2. Configuring Printer Properties to Split the A1 Page (Critical Step)

1.  Click on **"Printer Properties"**.
2.  Navigate to the **"Paper"** or **"Paper Settings"** tab.
3.  Set the **Page Size** to **A1** (e.g., $594.0 \times 841.0 \text{ mm}$).
4.  In the **"Layout"** section, set the **Sheet Size** to **A3**.
5.  Locate the **Position** boxes (X and Y coordinates) to define the four A3 sections. You must repeat the print/save process for each position. 

### **A1 Poster Split Coordinates**

| A3 Page Section | Position X (mm) | Position Y (mm) |
| :--- | :--- | :--- |
| **Top-Left** (Part 1) | **0.0** | **0.0** |
| **Top-Right** (Part 2) | **-297.0** | **0.0** |
| **Bottom-Left** (Part 3) | **0.0** | **-420.0** |
| **Bottom-Right** (Part 4) | **-297.0** | **-420.0** |

* **Action:** Print/Save as a separate PDF file (e.g., *Poster\_Part1.pdf*) after setting each coordinate pair.

![selecting positioning](assets/printing_2.png)

---

## 3. Printing the A3 PDF Sheets

To successfully print A3 on campus:

1.  You must be connected to the **VPN**.
2.  Open each of the four saved A3 PDFs (e.g., in a browser like Chrome).
3.  Initiate printing.
4.  Select the **Staff on LeedsPrint** print queue as the **Destination**.
5.  Click on **"More settings"**.
6.  Set the **Paper size** to **A3**. 
    * *Note: The user found this method more reliable than using the MyPrint portal.*

![using chrome to print](assets/printing_3.png)

---

## 4. Alternative Printing Options (If Laptop Fails)

If printing from your personal laptop fails or the print queue is unavailable, you have other options:

* Use a **wired computer** in the library.
* Print through the **Azure Virtual Desktop** (AVD).

* *(Refer to University IT services for up-to-date guidance on these options).*

---

## 5. Assembly

Each A3 sheet will print with a **border**.

* To create a fully joined-up A1 poster, you will need to **cut the borders off** and stick the four A3 sheets together with Sellotape.
