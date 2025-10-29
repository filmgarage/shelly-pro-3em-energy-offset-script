# Shelly Pro 3EM Energy Offset Script

A powerful script for the Shelly Pro 3EM that allows you to add custom offsets to energy readings and track consumption over specific periods with a convenient zero button.

## Features

✨ **Custom Energy Offsets** - Add or subtract values from each phase and total energy readings  
📊 **Home Assistant Integration** - Full history tracking with number entities  
🔄 **Zero Button** - Instantly reset displays to monitor specific time periods  
⚡ **Real-time Updates** - Values update every 10 seconds  
🎯 **Per-Phase Control** - Independent offsets for Phase A, B, C, and Total

## Use Cases

- **Utility Meter Alignment** - Match your Shelly readings to your utility company's meter
- **Period Monitoring** - Track consumption for specific events, days, or billing cycles
- **Solar Integration** - Compensate for solar production in your totals
- **Tenant Billing** - Set starting values for new tenants
- **Migration** - Transfer readings from an old energy monitor

## Quick Start

### Prerequisites

- Shelly Pro 3EM with updated firmware
- Access to Shelly web interface at `http://YOUR_SHELLY_IP`

### Installation

#### Step 1: Create Virtual Components

Navigate to **Settings → Virtual Components** in your Shelly web interface.

**Create 4 Number Components for Display:**

| ID  | Name                 | Unit | Min   | Max       |
|-----|----------------------|------|-------|-----------|
| 100 | Phase A Total Energy | kWh  | 0     | 999999999 |
| 101 | Phase B Total Energy | kWh  | 0     | 999999999 |
| 102 | Phase C Total Energy | kWh  | 0     | 999999999 |
| 103 | Total Energy         | kWh  | 0     | 999999999 |

**Create 4 Number Components for Offsets:**

| ID  | Name           | Unit | Min        | Max       |
|-----|----------------|------|------------|-----------|
| 200 | Phase A Offset | kWh  | -999999999 | 999999999 |
| 201 | Phase B Offset | kWh  | -999999999 | 999999999 |
| 202 | Phase C Offset | kWh  | -999999999 | 999999999 |
| 203 | Total Offset   | kWh  | -999999999 | 999999999 |

**Create 1 Button Component:**

| ID  | Name                 |
|-----|----------------------|
| 300 | Zero Energy Displays |

> **Important:** Click **Save** after adding each component!

#### Step 2: Install the Script

1. Navigate to **Settings → Scripts**
2. Click **+ Add Script**
3. Name it (e.g., "Energy Offset")
4. Copy and paste the contents of `shelly_pro_3em_energy_offset.js`
5. Click **Save**
6. Click **Start** to run the script

#### Step 3: Verify Operation

1. Return to **Settings → Virtual Components**
2. The 4 display components should show current energy values in kWh
3. The 4 offset components should be set to 0 by default
4. The "Zero Energy Displays" button should be visible

## How It Works

The script performs a simple calculation every 10 seconds:

```
Display Value (kWh) = (API Energy Value (Wh) ÷ 1000) + Offset (kWh)
```

**Example:**
- Shelly reports: 5,000,000 Wh
- Converted to kWh: 5,000 kWh
- Your offset: 2,500 kWh
- Display shows: 7,500 kWh

## Using the Zero Button

The zero button instantly resets all energy displays to 0.000 kWh, making it perfect for monitoring consumption over specific periods.

### How It Works

When pressed, the button:
1. Reads current energy values from all phases
2. Sets each offset to the negative of the current value
3. Results in all displays showing 0.000 kWh

**Important:** Your actual energy data is never deleted - the button simply adjusts offsets!

### Usage Methods

**Via Web Interface:**
1. Go to **Settings → Virtual Components**
2. Find "Zero Energy Displays" button
3. Click it
4. Wait 10 seconds - displays now show 0.000 kWh

**Via HTTP API:**
```bash
curl -X POST "http://YOUR_SHELLY_IP/rpc/Button.Trigger?id=300"
```

**Via Home Assistant:**

The button appears as: `button.shelly_pro_3em_XXXXXX_zero_energy_displays`

Use in automations:
```yaml
service: button.press
target:
  entity_id: button.shelly_pro_3em_XXXXXX_zero_energy_displays
```

### Use Case Examples

📅 **Daily Tracking**  
Press the button every morning to see daily consumption by evening

📊 **Weekly Monitoring**  
Zero on Monday morning, check total on Sunday evening

🎉 **Event Tracking**  
Press before a party, see exactly how much energy the event consumed

🔌 **Appliance Testing**  
Zero before turning on an appliance, measure its exact consumption

## Setting Offsets

Offsets allow you to adjust displayed values to match utility meters or set custom starting points.

### Via Web Interface

1. Navigate to **Settings → Virtual Components**
2. Find the offset you want to modify
3. Enter a value in kilowatt-hours (kWh)
   - Positive values increase the display
   - Negative values decrease the display
4. The display updates within 10 seconds

**Examples:**
- Enter `5` to add 5 kWh to the display
- Enter `-2.5` to subtract 2.5 kWh from the display

### Via HTTP API

```bash
# Set Phase A offset to 5 kWh
curl "http://YOUR_SHELLY_IP/rpc/Number.Set?id=200&value=5"

# Set Phase B offset to -2.5 kWh
curl "http://YOUR_SHELLY_IP/rpc/Number.Set?id=201&value=-2.5"

# Set Total offset to 12345 kWh
curl "http://YOUR_SHELLY_IP/rpc/Number.Set?id=203&value=12345"
```

### Calculating Offsets

To match a utility meter reading:

**Example:** Your utility meter shows 12,345 kWh, but Shelly shows 5,000 kWh

```
Offset = Utility Reading - Shelly Reading
Offset = 12,345 - 5,000 = 7,345 kWh
```

Set the Total Offset to `7345` kWh.

## Home Assistant Integration

Once added to Home Assistant, the script creates 9 entities automatically:

### Display Entities (Read-Only)
- `number.shelly_pro_3em_XXXXXX_phase_a_total_energy` 
- `number.shelly_pro_3em_XXXXXX_phase_b_total_energy` 
- `number.shelly_pro_3em_XXXXXX_phase_c_total_energy` 
- `number.shelly_pro_3em_XXXXXX_total_energy` 

### Offset Entities (Adjustable)
- `number.shelly_pro_3em_XXXXXX_phase_a_offset` 
- `number.shelly_pro_3em_XXXXXX_phase_b_offset` 
- `number.shelly_pro_3em_XXXXXX_phase_c_offset` 
- `number.shelly_pro_3em_XXXXXX_total_offset` 

### Button Entity
- `button.shelly_pro_3em_XXXXXX_zero_energy_displays`

### Dashboard Example

Add to your Home Assistant dashboard:

```yaml
type: entities
title: Energy Monitoring
entities:
  - entity: number.shelly_pro_3em_XXXXXX_phase_a_total_energy
    name: Phase A Energy
  - entity: number.shelly_pro_3em_XXXXXX_phase_b_total_energy
    name: Phase B Energy
  - entity: number.shelly_pro_3em_XXXXXX_phase_c_total_energy
    name: Phase C Energy
  - entity: number.shelly_pro_3em_XXXXXX_total_energy
    name: Total Energy
  - type: divider
  - entity: button.shelly_pro_3em_XXXXXX_zero_energy_displays
    name: Reset to Zero
```

### Automation Examples

**Automatic Daily Reset:**
```yaml
automation:
  - alias: "Reset Energy Displays Daily"
    trigger:
      platform: time
      at: "00:00:00"
    action:
      service: button.press
      target:
        entity_id: button.shelly_pro_3em_XXXXXX_zero_energy_displays
```

**Set Offset to Match Utility Meter:**
```yaml
automation:
  - alias: "Sync with Utility Meter"
    trigger:
      platform: time
      at: "06:00:00"
    action:
      service: number.set_value
      target:
        entity_id: number.shelly_pro_3em_XXXXXX_total_offset
      data:
        value: 12345  # Your utility meter reading
```

### Energy Dashboard Integration

All number entities have the proper `device_class: energy` and `state_class: total_increasing`, making them compatible with Home Assistant's Energy Dashboard:

1. Go to **Settings → Dashboards → Energy**
2. Click **Add Consumption**
3. Select your preferred entity (e.g., `number.shelly_pro_3em_XXXXXX_total_energy`)
4. Configure time period and start tracking!

## Troubleshooting

### Script Won't Start

**Symptoms:** Script shows error or won't start

**Solutions:**
- Verify all 9 virtual components are created (IDs: 100-103, 200-203, 300)
- Check that component IDs match exactly
- Ensure firmware is up to date
- Restart the Shelly device

### Energy Values Not Showing

**Symptoms:** Display components show 0 or no value

**Solutions:**
- Wait 10 seconds for the first update cycle
- Check the script log for errors (Settings → Scripts → View Log)
- Verify the Pro 3EM is measuring energy (check API: `http://YOUR_IP/rpc/EMData.GetStatus?id=0`)
- Confirm script is running (should show "Running" status)

### Zero Button Not Working

**Symptoms:** Clicking button doesn't reset displays

**Solutions:**
- Verify button component ID is 300
- Check script log after pressing button
- Wait 10 seconds for update cycle
- Ensure script is running

### Home Assistant Entities Not Appearing

**Symptoms:** Entities don't show up in Home Assistant

**Solutions:**
- Reload the Shelly integration (Settings → Devices & Services)
- Wait 2-3 minutes for entity discovery
- Check the entity registry (Settings → Devices & Services → Shelly → Device)
- Verify Shelly firmware is up to date

### Values Seem Incorrect

**Symptoms:** Displayed values don't match expectations

**Solutions:**
- Check offsets are set correctly (Settings → Virtual Components)
- Verify Shelly is measuring correctly via API
- Confirm you're using kWh (not Wh) for offset values
- Check calculation: Display = (API Energy ÷ 1000) + Offset

## Common Scenarios

### Scenario 1: Match Utility Meter

Your utility meter shows 15,234.5 kWh, but Shelly shows 234.5 kWh.

**Solution:**
```
Offset = 15,234.5 - 234.5 = 15,000 kWh
```
Set Total Offset to `15000` kWh.

### Scenario 2: Start Fresh Each Month

You want to track monthly consumption from zero.

**Solution:**
- Press the Zero button on the 1st of each month
- Or create a Home Assistant automation to do it automatically

### Scenario 3: Monitor Party Consumption

You're hosting a party and want to see exactly how much energy it uses.

**Solution:**
1. Press Zero button before guests arrive
2. Check Total Energy after party ends
3. That's your party's energy consumption!

### Scenario 4: Solar Production Compensation

Your solar system produced 500 kWh this month.

**Solution:**
Set Total Offset to `-500` kWh to subtract production from consumption.

## API Reference

### Get Current Energy Values

```bash
curl "http://YOUR_SHELLY_IP/rpc/EMData.GetStatus?id=0"
```

Returns raw energy values in Wh (Watt-hours).

### Get Display Values

```bash
# Get Phase A display value
curl "http://YOUR_SHELLY_IP/rpc/Number.GetStatus?id=100"

# Get Total display value
curl "http://YOUR_SHELLY_IP/rpc/Number.GetStatus?id=103"
```

### Set Offsets

```bash
# Set Phase A offset
curl "http://YOUR_SHELLY_IP/rpc/Number.Set?id=200&value=OFFSET_IN_KWH"

# Set Total offset
curl "http://YOUR_SHELLY_IP/rpc/Number.Set?id=203&value=OFFSET_IN_KWH"
```

### Trigger Zero Button

```bash
curl -X POST "http://YOUR_SHELLY_IP/rpc/Button.Trigger?id=300"
```

## Technical Details

**Update Interval:** 10 seconds  
**Component IDs:**
- Display: 100-103 (Number components, kWh)
- Offsets: 200-203 (Number components, kWh)
- Zero Button: 300 (Button component)

**Calculation:**
```javascript
Display (kWh) = (API Energy (Wh) ÷ 1000) + Offset (kWh)
```

**Data Persistence:**
- Offsets are stored in Shelly's virtual components
- Survive reboots and script restarts
- Can be backed up via Shelly configuration export

## Contributing

Contributions are welcome! Please feel free to submit a Pull Request.

## License

MIT License - Feel free to use and modify as needed.

## Support

If you encounter issues:
1. Check the Troubleshooting section above
2. Review the script log (Settings → Scripts → View Log)
3. Open an issue on GitHub with:
   - Shelly firmware version
   - Script log output
   - Description of the problem

## Changelog

See [CHANGELOG.md](CHANGELOG.md) for version history.

---

**Made with ❤️ for the Shelly community**
