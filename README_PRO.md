# Shelly Pro 3EM Energy Offset Script

Quick setup for experienced users.

## Installation

1. **Create Virtual Components** (Settings → Virtual Components):
   - 4 Number components for display (kWh): IDs 100-103
   - 4 Number components for offsets (kWh): IDs 200-203
   - 1 Button component for zero: ID 300

2. **Install Script** (Settings → Scripts):
   - Create new script
   - Paste `shelly_pro_3em_energy_offset.js`
   - **Modify IDs in script** if your component IDs differ from default
   - Save and Start

3. **Wait 10 seconds** for initial values to populate

## Default Component IDs

```javascript
virtualIds: {
  phaseA: 100,      phaseB: 101,      phaseC: 102,      total: 103,
  offsetA: 200,     offsetB: 201,     offsetC: 202,     offsetTotal: 203,
  zeroButton: 300
}
```

## Quick Usage

- **Set offsets**: Virtual Components → modify offset values (in kWh)
- **Zero displays**: Press button ID 300 (sets offsets to -current values)
- **HTTP API**: `http://IP/rpc/Number.Set?id=200&value=VALUE`

## Troubleshooting

Not working? See [Extended README](README_EXTENDED.md) for detailed setup, Home Assistant integration, and troubleshooting.

---

**Formula**: Display (kWh) = (API Energy / 1000) + Offset  
**Updates**: Every 10 seconds
