# TransitOps ![Hackathon Project](https://img.shields.io/badge/type-Hackathon%20Project-orange)
## Smart Transport Operations Platform

---

> Digitize the entire lifecycle of a transport fleet — vehicles, drivers, dispatch, maintenance, fuel, and expenses — in one rule-driven system. No spreadsheets. No manual logbooks. No missed license renewals.

---

> [!IMPORTANT]
> **Public Testing Credentials**
>
> **Live Deployed Site**: [TransitOps Web App](https://transit-ops-kappa.vercel.app/)
>
> To test the live deployed version, you can log in using any of the following pre-seeded role accounts:
> * **Fleet Manager**: `manager@transitops.in` (Password: `password123`)
> * **Dispatcher**: `dispatcher@transitops.in` (Password: `password123`)
> * **Safety Officer**: `safety@transitops.in` (Password: `password123`)
> * **Financial Analyst**: `analyst@transitops.in` (Password: `password123`)

---

## The Problem

Most logistics and fleet operators — from regional trucking companies to last-mile delivery fleets — still run their day-to-day operations on spreadsheets, WhatsApp groups, and paper logbooks.

That means:

- **Double-booked vehicles** because two dispatchers assigned the same truck at the same time.
- **Drivers sent out with expired licenses** because nobody cross-checked the expiry date before dispatch.
- **Vehicles that should be in the workshop still showing up as "available"** because the maintenance log lives in a separate Excel file.
- **Overloaded vehicles** because cargo weight was never checked against the vehicle's rated capacity.
- **No real picture of cost** — fuel bills, toll receipts, and repair invoices sit in three different places, so nobody actually knows the operational cost or ROI of a given vehicle.

**TransitOps** replaces all of this with a single, rule-enforced system: register a vehicle and driver once, and the platform handles every status transition, every eligibility check, and every cost roll-up automatically.

---

## Target Users

<div align="center">

<table width="100%" style="text-align: center; border-collapse: collapse;">
  <thead>
    <tr style="border-bottom: 2px solid #ccc;">
      <th style="padding: 10px;">Role</th>
      <th style="padding: 10px;">What they do on TransitOps</th>
    </tr>
  </thead>
  <tbody>
    <tr style="border-bottom: 1px solid #ddd;">
      <td style="padding: 10px;"><b>Fleet Manager</b></td>
      <td style="padding: 10px;">Oversees the fleet lifecycle end-to-end — vehicle onboarding, retirement, maintenance scheduling, and overall operational efficiency.</td>
    </tr>
    <tr style="border-bottom: 1px solid #ddd;">
      <td style="padding: 10px;"><b>Dispatcher</b></td>
      <td style="padding: 10px;">Creates trips, assigns available vehicles and drivers, and tracks active deliveries through to completion.</td>
    </tr>
    <tr style="border-bottom: 1px solid #ddd;">
      <td style="padding: 10px;"><b>Safety Officer</b></td>
      <td style="padding: 10px;">Monitors license validity, driver compliance, and safety scores — the last line of defense before an unsafe driver or vehicle gets dispatched.</td>
    </tr>
    <tr style="border-bottom: 1px solid #ddd;">
      <td style="padding: 10px;"><b>Financial Analyst</b></td>
      <td style="padding: 10px;">Reviews fuel spend, maintenance costs, and per-vehicle profitability to guide fleet investment decisions.</td>
    </tr>
  </tbody>
</table>

<p style="margin-top: 10px;"><b>Table 1. Target user roles and responsibilities.</b></p>

</div>

All four roles work off the **same dataset**, viewed through **role-based access control (RBAC)** — a Financial Analyst sees costs and ROI, a Safety Officer sees license/compliance flags, and so on.

---

## Operational Scenarios

### Scenario 1 — The Overloaded Truck That Never Left the Yard

A dispatcher tries to book Van-05 (max capacity 500 kg) for a delivery with 650 kg of cargo.

**Without TransitOps:** The load sheet is a spreadsheet cell. Nobody notices the mismatch until the vehicle is already overloaded on the highway — a safety violation and a potential fine.

**With TransitOps:** The trip creation form validates cargo weight against the vehicle's Maximum Load Capacity in real time. 650 kg > 500 kg → the system blocks dispatch before the trip is ever created.

---

### Scenario 2 — The Driver With an Expired License

A driver named Rakesh is scheduled for a same-day trip, but his license expired four days ago.

**Without TransitOps:** The expiry date lives in a paper file the Safety Officer checks once a month. Rakesh drives anyway.

**With TransitOps:** Rakesh's status is checked automatically at the point of assignment. An expired license (or a `Suspended` status) removes him from the driver selection pool entirely — he simply cannot be assigned.

---

### Scenario 3 — The Vehicle That Was Supposed to Be in the Shop

A vehicle is due for an oil change, but a dispatcher accidentally tries to send it out on a trip.

**Without TransitOps:** The maintenance log and the dispatch sheet are two different files that never talk to each other.

**With TransitOps:** The moment a maintenance record is created for that vehicle, its status automatically flips to `In Shop`, which instantly removes it from the dispatch/driver selection pool — no manual double-checking required.

---

### Scenario 4 — Knowing the Real Cost of a Vehicle

The Financial Analyst wants to know whether Van-05 is actually profitable.

**Without TransitOps:** Fuel receipts, toll slips, and a maintenance invoice are gathered manually from three sources at month-end.

**With TransitOps:** Every fuel log and expense entry against Van-05 rolls up automatically into its Operational Cost, and the Reports module computes its **Vehicle ROI** on demand:

```
Vehicle ROI = (Revenue − (Maintenance + Fuel)) / Acquisition Cost
```

---

## Platform Choice: Single Responsive Web App with RBAC

Unlike a rider-facing product, every TransitOps user — Fleet Manager, Driver, Safety Officer, Financial Analyst — works at a desk or on a tablet inside an operations office or yard, not on the move making split-second taps. A single **responsive web application** with **role-based views** is the right shape here:

- One codebase, one dataset, one source of truth.
- Role-Based Access Control determines what each login can see and do — a Driver cannot edit acquisition cost; a Financial Analyst cannot dispatch a trip.
- Responsive layout means it works equally well on a dispatcher's desktop monitor and a Safety Officer's tablet during a yard inspection.

---

## Application Workflow

### Trip Lifecycle

```
Draft → Dispatched → Completed → Cancelled
```

**Step 1 — Create Trip (Draft)**
Select source, destination, an *available* vehicle, an *available* driver, cargo weight, and planned distance.

**Step 2 — Validation (automatic)**
System checks, in order:
- Vehicle is not `Retired` or `In Shop`
- Driver's license is valid and status is not `Suspended`
- Neither vehicle nor driver is already `On Trip`
- Cargo weight ≤ vehicle's Maximum Load Capacity

Any failed check blocks the trip from being dispatched.

**Step 3 — Dispatch**
Trip moves to `Dispatched`. Vehicle and Driver statuses automatically flip to `On Trip`.

**Step 4 — Completion or Cancellation**
- **Completed:** Final odometer and fuel consumed are logged. Vehicle and Driver automatically return to `Available`.
- **Cancelled:** Vehicle and Driver automatically restored to `Available`.

**Step 5 — Reports Refresh**
Fuel Efficiency, Operational Cost, and Fleet Utilization recompute using the latest trip and fuel log data.

---

### Role-Based Dashboard Views

<div align="center">

<table width="100%" style="text-align: center; border-collapse: collapse;">
  <thead>
    <tr style="border-bottom: 2px solid #ccc;">
      <th style="padding: 10px;">Role</th>
      <th style="padding: 10px;">Primary View</th>
    </tr>
  </thead>
  <tbody>
    <tr style="border-bottom: 1px solid #ddd;">
      <td style="padding: 10px;"><b>Fleet Manager</b></td>
      <td style="padding: 10px;">Fleet-wide KPIs — Active/Available Vehicles, Vehicles in Maintenance, Fleet Utilization (%) — plus filters by vehicle type, status, and region.</td>
    </tr>
    <tr style="border-bottom: 1px solid #ddd;">
      <td style="padding: 10px;"><b>Dispatcher</b></td>
      <td style="padding: 10px;">Trip creation screen and a live view of active/pending trips they are assigned to or dispatching.</td>
    </tr>
    <tr style="border-bottom: 1px solid #ddd;">
      <td style="padding: 10px;"><b>Safety Officer</b></td>
      <td style="padding: 10px;">Driver roster with license expiry dates, safety scores, and current status (<code>Available</code> / <code>On Trip</code> / <code>Off Duty</code> / <code>Suspended</code>) flagged for compliance risk.</td>
    </tr>
    <tr style="border-bottom: 1px solid #ddd;">
      <td style="padding: 10px;"><b>Financial Analyst</b></td>
      <td style="padding: 10px;">Reports &amp; Analytics — Fuel Efficiency, Operational Cost, and Vehicle ROI, exportable to CSV.</td>
    </tr>
  </tbody>
</table>

<p style="margin-top: 10px;"><b>Table 2. Role-based dashboard views.</b></p>

</div>

---

## Mandatory Business Rules

<div align="center">

<table width="100%" style="text-align: center; border-collapse: collapse;">
  <thead>
    <tr style="border-bottom: 2px solid #ccc;">
      <th style="padding: 10px;">#</th>
      <th style="padding: 10px;">Rule</th>
    </tr>
  </thead>
  <tbody>
    <tr style="border-bottom: 1px solid #ddd;">
      <td style="padding: 10px;">1</td>
      <td style="padding: 10px;">Vehicle registration number must be unique.</td>
    </tr>
    <tr style="border-bottom: 1px solid #ddd;">
      <td style="padding: 10px;">2</td>
      <td style="padding: 10px;">Retired or In Shop vehicles must never appear in the dispatch selection.</td>
    </tr>
    <tr style="border-bottom: 1px solid #ddd;">
      <td style="padding: 10px;">3</td>
      <td style="padding: 10px;">Drivers with expired licenses or Suspended status cannot be assigned to trips.</td>
    </tr>
    <tr style="border-bottom: 1px solid #ddd;">
      <td style="padding: 10px;">4</td>
      <td style="padding: 10px;">A driver or vehicle already marked On Trip cannot be assigned to another trip.</td>
    </tr>
    <tr style="border-bottom: 1px solid #ddd;">
      <td style="padding: 10px;">5</td>
      <td style="padding: 10px;">Cargo Weight must not exceed the vehicle's maximum load capacity.</td>
    </tr>
    <tr style="border-bottom: 1px solid #ddd;">
      <td style="padding: 10px;">6</td>
      <td style="padding: 10px;">Dispatching a trip automatically changes both the vehicle and driver status to On Trip.</td>
    </tr>
    <tr style="border-bottom: 1px solid #ddd;">
      <td style="padding: 10px;">7</td>
      <td style="padding: 10px;">Completing a trip automatically changes both the vehicle and driver status back to Available.</td>
    </tr>
    <tr style="border-bottom: 1px solid #ddd;">
      <td style="padding: 10px;">8</td>
      <td style="padding: 10px;">Cancelling a dispatched trip restores the vehicle and driver to Available.</td>
    </tr>
    <tr style="border-bottom: 1px solid #ddd;">
      <td style="padding: 10px;">9</td>
      <td style="padding: 10px;">Creating an active maintenance record automatically changes vehicle status to In Shop.</td>
    </tr>
    <tr style="border-bottom: 1px solid #ddd;">
      <td style="padding: 10px;">10</td>
      <td style="padding: 10px;">Closing maintenance restores the vehicle to Available (unless retired).</td>
    </tr>
  </tbody>
</table>

<p style="margin-top: 10px;"><b>Table 3. Mandatory business rules enforced by the platform.</b></p>

</div>

---

## Reports & Analytics — Formulas

<div align="center">

<table width="100%" style="text-align: center; border-collapse: collapse;">
  <thead>
    <tr style="border-bottom: 2px solid #ccc;">
      <th style="padding: 10px;">Metric</th>
      <th style="padding: 10px;">Formula</th>
    </tr>
  </thead>
  <tbody>
    <tr style="border-bottom: 1px solid #ddd;">
      <td style="padding: 10px;"><b>Fuel Efficiency</b></td>
      <td style="padding: 10px;">Distance ÷ Fuel</td>
    </tr>
    <tr style="border-bottom: 1px solid #ddd;">
      <td style="padding: 10px;"><b>Fleet Utilization</b></td>
      <td style="padding: 10px;">(Vehicles On Trip ÷ Total Active Vehicles) × 100</td>
    </tr>
    <tr style="border-bottom: 1px solid #ddd;">
      <td style="padding: 10px;"><b>Operational Cost</b></td>
      <td style="padding: 10px;">Fuel Cost + Maintenance Cost (per vehicle)</td>
    </tr>
    <tr style="border-bottom: 1px solid #ddd;">
      <td style="padding: 10px;"><b>Vehicle ROI</b></td>
      <td style="padding: 10px;">(Revenue − (Maintenance + Fuel)) ÷ Acquisition Cost</td>
    </tr>
  </tbody>
</table>

<p style="margin-top: 10px;"><b>Table 4. Reports and analytics formulas.</b></p>

</div>

CSV export is supported for all reports; PDF export is a bonus feature.

---

## Database Entities

<div align="center">

<table width="100%" style="text-align: center; border-collapse: collapse;">
  <thead>
    <tr style="border-bottom: 2px solid #ccc;">
      <th style="padding: 10px;">Entity</th>
      <th style="padding: 10px;">Key Fields</th>
    </tr>
  </thead>
  <tbody>
    <tr style="border-bottom: 1px solid #ddd;">
      <td style="padding: 10px;"><b>Users</b></td>
      <td style="padding: 10px;">Email, password (hashed), role</td>
    </tr>
    <tr style="border-bottom: 1px solid #ddd;">
      <td style="padding: 10px;"><b>Roles</b></td>
      <td style="padding: 10px;">Fleet Manager, Driver, Safety Officer, Financial Analyst</td>
    </tr>
    <tr style="border-bottom: 1px solid #ddd;">
      <td style="padding: 10px;"><b>Vehicles</b></td>
      <td style="padding: 10px;">Registration Number (unique), Name/Model, Type, Max Load Capacity, Odometer, Acquisition Cost, Status (<code>Available</code> / <code>On Trip</code> / <code>In Shop</code> / <code>Retired</code>)</td>
    </tr>
    <tr style="border-bottom: 1px solid #ddd;">
      <td style="padding: 10px;"><b>Drivers</b></td>
      <td style="padding: 10px;">Name, License Number, License Category, License Expiry Date, Contact Number, Safety Score, Status (<code>Available</code> / <code>On Trip</code> / <code>Off Duty</code> / <code>Suspended</code>)</td>
    </tr>
    <tr style="border-bottom: 1px solid #ddd;">
      <td style="padding: 10px;"><b>Trips</b></td>
      <td style="padding: 10px;">Source, Destination, Vehicle, Driver, Cargo Weight, Planned Distance, Status (<code>Draft</code> / <code>Dispatched</code> / <code>Completed</code> / <code>Cancelled</code>)</td>
    </tr>
    <tr style="border-bottom: 1px solid #ddd;">
      <td style="padding: 10px;"><b>Maintenance Logs</b></td>
      <td style="padding: 10px;">Vehicle, Service Type, Open/Close Status, Cost</td>
    </tr>
    <tr style="border-bottom: 1px solid #ddd;">
      <td style="padding: 10px;"><b>Fuel Logs</b></td>
      <td style="padding: 10px;">Vehicle, Liters, Cost, Date</td>
    </tr>
    <tr style="border-bottom: 1px solid #ddd;">
      <td style="padding: 10px;"><b>Expenses</b></td>
      <td style="padding: 10px;">Vehicle, Type (Toll / Other), Amount, Date</td>
    </tr>
  </tbody>
</table>

<p style="margin-top: 10px;"><b>Table 5. Database entities and key fields.</b></p>

</div>

---

## Example Workflow (End-to-End)

<div align="center">

<table width="100%" style="text-align: center; border-collapse: collapse;">
  <thead>
    <tr style="border-bottom: 2px solid #ccc;">
      <th style="padding: 10px;">Step</th>
      <th style="padding: 10px;">Action</th>
      <th style="padding: 10px;">Resulting State</th>
    </tr>
  </thead>
  <tbody>
    <tr style="border-bottom: 1px solid #ddd;">
      <td style="padding: 10px;">1</td>
      <td style="padding: 10px;">Register vehicle <code>Van-05</code>, max capacity 500 kg</td>
      <td style="padding: 10px;">Status = Available</td>
    </tr>
    <tr style="border-bottom: 1px solid #ddd;">
      <td style="padding: 10px;">2</td>
      <td style="padding: 10px;">Register driver <code>Alex</code> with a valid license</td>
      <td style="padding: 10px;">Driver = Available</td>
    </tr>
    <tr style="border-bottom: 1px solid #ddd;">
      <td style="padding: 10px;">3</td>
      <td style="padding: 10px;">Create a trip with Cargo Weight = 450 kg</td>
      <td style="padding: 10px;">450 kg ≤ 500 kg → validation passes</td>
    </tr>
    <tr style="border-bottom: 1px solid #ddd;">
      <td style="padding: 10px;">4</td>
      <td style="padding: 10px;">Dispatch the trip</td>
      <td style="padding: 10px;">Vehicle &amp; Driver → On Trip</td>
    </tr>
    <tr style="border-bottom: 1px solid #ddd;">
      <td style="padding: 10px;">5</td>
      <td style="padding: 10px;">Complete the trip (enter final odometer + fuel consumed)</td>
      <td style="padding: 10px;">Vehicle &amp; Driver → Available</td>
    </tr>
    <tr style="border-bottom: 1px solid #ddd;">
      <td style="padding: 10px;">6</td>
      <td style="padding: 10px;">Create a maintenance record (e.g., Oil Change)</td>
      <td style="padding: 10px;">Vehicle → In Shop, hidden from dispatch</td>
    </tr>
    <tr style="border-bottom: 1px solid #ddd;">
      <td style="padding: 10px;">7</td>
      <td style="padding: 10px;">Reports refresh</td>
      <td style="padding: 10px;">Operational cost &amp; fuel efficiency recomputed from the latest trip and fuel log</td>
    </tr>
  </tbody>
</table>

<p style="margin-top: 10px;"><b>Table 6. Example end-to-end workflow.</b></p>

</div>

---

## Tech Stack

<div align="center">

<table width="100%" style="text-align: center; border-collapse: collapse;">
  <thead>
    <tr style="border-bottom: 2px solid #ccc;">
      <th style="padding: 10px;">Layer</th>
      <th style="padding: 10px;">Technology</th>
      <th style="padding: 10px;">Reason</th>
    </tr>
  </thead>
  <tbody>
    <tr style="border-bottom: 1px solid #ddd;">
      <td style="padding: 10px;">Frontend</td>
      <td style="padding: 10px;">React.js (Vite)</td>
      <td style="padding: 10px;">Component-driven UI, fastest to build role-based dashboards with, huge ecosystem</td>
    </tr>
    <tr style="border-bottom: 1px solid #ddd;">
      <td style="padding: 10px;">Backend</td>
      <td style="padding: 10px;">Node.js + Express</td>
      <td style="padding: 10px;">Lightweight REST API, minimal boilerplate, easy to scaffold within an 8-hour window</td>
    </tr>
    <tr style="border-bottom: 1px solid #ddd;">
      <td style="padding: 10px;">Database</td>
      <td style="padding: 10px;">MongoDB</td>
      <td style="padding: 10px;">Document store — flexible schema, fast to iterate on during a hackathon, no migration overhead when fields change</td>
    </tr>
    <tr style="border-bottom: 1px solid #ddd;">
      <td style="padding: 10px;">ODM</td>
      <td style="padding: 10px;">Mongoose</td>
      <td style="padding: 10px;">Schema validation and model layer on top of MongoDB, keeps status-enum and relationship logic enforced in code</td>
    </tr>
    <tr style="border-bottom: 1px solid #ddd;">
      <td style="padding: 10px;">Auth</td>
      <td style="padding: 10px;">JWT-based email/password auth</td>
      <td style="padding: 10px;">Stateless, simple to implement RBAC middleware around</td>
    </tr>
    <tr style="border-bottom: 1px solid #ddd;">
      <td style="padding: 10px;">Charts</td>
      <td style="padding: 10px;">Recharts</td>
      <td style="padding: 10px;">React-native charting for dashboard KPIs and analytics visualizations</td>
    </tr>
    <tr style="border-bottom: 1px solid #ddd;">
      <td style="padding: 10px;">Hosting</td>
      <td style="padding: 10px;">Vercel (frontend) + Render (backend) + MongoDB Atlas (database)</td>
      <td style="padding: 10px;">Free tier across the board, zero DevOps overhead for an 8-hour build</td>
    </tr>
  </tbody>
</table>

<p style="margin-top: 10px;"><b>Table 7. Technology stack and rationale.</b></p>

</div>

**Total infrastructure cost: ₹0** (all free-tier services).

---

## Local Development Setup

### 1. Prerequisites
- **Node.js (v18+) & npm**
- **MongoDB** — either a local instance or a free [MongoDB Atlas](https://www.mongodb.com/cloud/atlas) cluster
- **Git**

### 2. Clone the Repository
```bash
git clone <your-repo-url>
cd transitops
```

### 3. Backend Setup
```bash
cd backend
npm install
```
Create a `.env` file in `backend/` with:
```
MONGODB_URI=<your-mongodb-connection-string>
JWT_SECRET=<your-secret-key>
PORT=5000
```
Seed demo data, then start the server:
```bash
npm run seed
npm run dev
```
The API will be live at `http://localhost:5000`.

### 4. Frontend Setup
```bash
cd ../frontend
npm install
npm run dev
```
Open `http://localhost:5173` in your browser.

### 5. Login
Use the seeded demo credentials to explore role-specific dashboards:
- **Fleet Manager**: `manager@transitops.in` / `password123`
- **Dispatcher**: `dispatcher@transitops.in` / `password123`
- **Safety Officer**: `safety@transitops.in` / `password123`
- **Financial Analyst**: `analyst@transitops.in` / `password123`

---

## Demo Flow

A 5-minute walkthrough to demonstrate the complete TransitOps lifecycle:

**1. Register Assets (Fleet Manager)**
Log in as Fleet Manager → register a vehicle (`Van-05`, 500 kg capacity) → register a driver (`Alex`) with a valid license.

**2. Attempt an Overloaded Trip (Dispatcher)**
Log in as Dispatcher → try creating a trip with cargo weight above 500 kg → system blocks it, demonstrating the cargo-capacity rule.

**3. Dispatch a Valid Trip (Dispatcher)**
Create a trip with 450 kg cargo → dispatch it → observe both Vehicle and Driver flip to `On Trip` in real time on the Fleet Manager's dashboard.

**4. Attempt to Reassign a Busy Asset (Dispatcher)**
Try assigning the same vehicle or driver to a second trip → system blocks it, since both are already `On Trip`.

**5. Complete the Trip and Trigger Maintenance (Fleet Manager)**
Complete the trip (Vehicle & Driver → `Available`) → create a maintenance record for the same vehicle → watch it disappear from the dispatch pool as its status flips to `In Shop`.

**6. Review the Numbers (Financial Analyst)**
Log in as Financial Analyst → view Fuel Efficiency, Operational Cost, and Vehicle ROI for `Van-05`, updated from the trip and fuel log just created → export the report to CSV.

---

## Assumptions & Scope

### Assumptions

<div align="center">

<table width="100%" style="text-align: center; border-collapse: collapse;">
  <thead>
    <tr style="border-bottom: 2px solid #ccc;">
      <th style="padding: 10px;">Assumption</th>
      <th style="padding: 10px;">Detail</th>
    </tr>
  </thead>
  <tbody>
    <tr style="border-bottom: 1px solid #ddd;">
      <td style="padding: 10px;">Single organization</td>
      <td style="padding: 10px;">The platform is scoped to one organization's fleet, not a multi-tenant SaaS.</td>
    </tr>
    <tr style="border-bottom: 1px solid #ddd;">
      <td style="padding: 10px;">Manual data entry</td>
      <td style="padding: 10px;">Odometer, fuel, and expense figures are entered by users, not pulled from IoT/telematics hardware.</td>
    </tr>
    <tr style="border-bottom: 1px solid #ddd;">
      <td style="padding: 10px;">Regional scope</td>
      <td style="padding: 10px;">"Region" is treated as a free-text/enum field for filtering, without live GPS tracking.</td>
    </tr>
    <tr style="border-bottom: 1px solid #ddd;">
      <td style="padding: 10px;">Revenue input</td>
      <td style="padding: 10px;">Revenue (for ROI calculation) is entered manually per vehicle/trip, since the spec does not define an automated revenue source.</td>
    </tr>
  </tbody>
</table>

<p style="margin-top: 10px;"><b>Table 8. Project assumptions.</b></p>

</div>

### Out of Scope

<div align="center">

<table width="100%" style="text-align: center; border-collapse: collapse;">
  <thead>
    <tr style="border-bottom: 2px solid #ccc;">
      <th style="padding: 10px;">Excluded</th>
      <th style="padding: 10px;">Reason</th>
    </tr>
  </thead>
  <tbody>
    <tr style="border-bottom: 1px solid #ddd;">
      <td style="padding: 10px;">Live GPS/telematics tracking</td>
      <td style="padding: 10px;">Not part of the mandatory or bonus feature list</td>
    </tr>
    <tr style="border-bottom: 1px solid #ddd;">
      <td style="padding: 10px;">Multi-organization / tenant support</td>
      <td style="padding: 10px;">Single fleet operator scope only</td>
    </tr>
    <tr style="border-bottom: 1px solid #ddd;">
      <td style="padding: 10px;">Native mobile app</td>
      <td style="padding: 10px;">Spec calls for a responsive web interface</td>
    </tr>
  </tbody>
</table>

<p style="margin-top: 10px;"><b>Table 9. Out-of-scope items and rationale.</b></p>

</div>

### Future Scope

<div align="center">

<table width="100%" style="text-align: center; border-collapse: collapse;">
  <thead>
    <tr style="border-bottom: 2px solid #ccc;">
      <th style="padding: 10px;">Area</th>
      <th style="padding: 10px;">Detail</th>
    </tr>
  </thead>
  <tbody>
    <tr style="border-bottom: 1px solid #ddd;">
      <td style="padding: 10px;">Telematics integration</td>
      <td style="padding: 10px;">Auto-capture odometer and location instead of manual entry</td>
    </tr>
    <tr style="border-bottom: 1px solid #ddd;">
      <td style="padding: 10px;">Predictive maintenance</td>
      <td style="padding: 10px;">Flag vehicles likely to need service based on usage patterns</td>
    </tr>
    <tr style="border-bottom: 1px solid #ddd;">
      <td style="padding: 10px;">Route optimization</td>
      <td style="padding: 10px;">Suggest optimal source-destination routing at trip creation</td>
    </tr>
    <tr style="border-bottom: 1px solid #ddd;">
      <td style="padding: 10px;">Multi-tenant support</td>
      <td style="padding: 10px;">Extend the platform to serve multiple fleet operators</td>
    </tr>
  </tbody>
</table>

<p style="margin-top: 10px;"><b>Table 10. Future scope items.</b></p>

</div>

---

## Mockup / Design Reference

Excalidraw wireframe: [View Mockup](https://link.excalidraw.com/l/65VNwvy7c4X/1FHGDNgD2td)