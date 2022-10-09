The following snippet contains the code currently used to generate the `ship-codes.json` file:

```js
var rsiShips = null;
var sanitizeShipMatrix = (allShips) => {
    // Map Properties
    allShips = allShips.map((ship) => {

        // Trim source data
        ship.name = ship.name.trim();
        ship.manufacturer.name = ship.manufacturer.name.trim();
        ship.manufacturer.code = ship.manufacturer.code.trim();
        
        // Ship name fixes
        ship.name = ship.name.replace(/ Best In Show Edition$/gi, '');
        // ship.name = ship.name.replace(/ Edition$/gi, '');
        ship.name = ship.name.replace(/ w\/C8X$/gi, '');
        ship.name = ship.name.replace('Pirate Gladius', 'Gladius Pirate');
        ship.name = ship.name.replace('Super Hornet Heartseeker', 'Hornet Heartseeker');
        ship.name = ship.name.replace(/^Argo /gi, '');
        ship.name = ship.name.replace(/^Mole/gi, 'MOLE');
        ship.name = ship.name.replace(/^Anvil /gi, '');
        ship.name = ship.name.replace(/^Aegis /gi, '');
        ship.name = ship.name.replace(/^Drake /gi, '');
        ship.name = ship.name.replace(/^Origin /gi, '');
        ship.name = ship.name.replace(/^P72 /gi, 'P-72 ');
        ship.name = ship.name.replace(/^P-72Archimedes/gi, 'P-72 Archimedes');
        ship.name = ship.name.replace(/^Mercury$/gi, 'Mercury Star Runner');
        ship.name = ship.name.replace(/^Genesis$/gi, 'Genesis Starliner');
        ship.name = ship.name.replace(/^([a-z][0-9]) Hercules$/gi, 'Hercules Starlifter $1');
        
        // Hornet
        // Hercules
        // Starliner
        
        // Manufacturer fixes
        ship.manufacturer.name = ship.manufacturer.name.replace(/^MISC$/gi, 'Musashi Industrial & Starflight Concern');
        ship.manufacturer.name = ship.manufacturer.name.replace(/^Banu$/gi, 'Banu Souli');
        ship.manufacturer.name = ship.manufacturer.name.replace(/^Tumbril$/gi, 'Tumbril Land Systems');
        ship.manufacturer.code = ship.manufacturer.code.replace(/^XNAA$/gi, 'XIAN');
        if (ship.manufacturer.code == 'ESPR') {
            if (ship.name == 'Glaive') { ship.manufacturer.code = 'VNCL'; ship.manufacturer.name = 'Vanduul' }
            if (ship.name == 'Blade')  { ship.manufacturer.code = 'VNCL'; ship.manufacturer.name = 'Vanduul' }
        }
        ship.manufacturer.code = ship.manufacturer.code.replace(/^CRUS$/gi, 'CRSD');
        
        // Generate a ship code
        ship.code = ship.manufacturer.code + '_' + ship.name.replace(/[^a-z0-9]/gi, '_').replace('__','_');
        
        // Ship code fixes
        ship.code = ship.code.replace(/F7([a-z])_Hornet/gi, 'Hornet_F7$1');
        ship.code = ship.code.replace(/F7([a-z])_([a-z]+)_(Super_)?Hornet(.*)$/gi, 'Hornet_F7$1$2');
        ship.code = ship.code.replace(/^CRSD_/, 'CRUS_');
        ship.code = ship.code.replace('KRIG_P_72', 'KRIG_P72');
        ship.code = ship.code.replace('KRIG_P_52', 'KRIG_P52');
        ship.code = ship.code.replace('ORIG_600i_Explorer', 'ORIG_600i');
        ship.code = ship.code.replace('RSI_Ursa_Rover_Fortuna', 'RSI_Ursa_Rover_Emerald');
        ship.code = ship.code.replace('San_tok_y_i', 'Santok_yai');
        ship.code = ship.code.replace('Khartu_Al', 'Scout');
        ship.code = ship.code.replace('ORIG_M50', 'ORIG_m50');
        ship.code = ship.code.replace('ORIG_890_Jump', 'ORIG_890Jump');
        ship.code = ship.code.replace('ARGO_MPUV_Cargo', 'ARGO_MPUV');
        ship.code = ship.code.replace('ARGO_MPUV_Personnel', 'ARGO_MPUV_Transport');
        ship.code = ship.code.replace('MISC_Reliant_Kore', 'MISC_Reliant');
        ship.code = ship.code.replace('DRAK_Dragonfly_Black', 'DRAK_Dragonfly');
        ship.code = ship.code.replace('CRUS_Mercury_Star_Runner', 'CRUS_Star_Runner');
        ship.code = ship.code.replace('AEGS_Vanguard_Warden', 'AEGS_Vanguard');
        ship.code = ship.code.replace('DRAK_Dragonfly_Yellowjacket', 'DRAK_Dragonfly_Yellow');
        if (ship.name == 'F7C-M Hornet Heartseeker') ship.code = 'ANVL_Hornet_F7CM_Heartseeker';
        
        return {
            ship_code: ship.code.trim(),
            ship_name: ship.name.trim(),
            manufacturer_code: ship.manufacturer.code,
            manufacturer_name: ship.manufacturer.name
        }
    });

    // Add Missing ships
    allShips.push({
        ship_code: 'ORIG_600i_Executive_Edition',
        ship_name: '600i Executive Edition',
        manufacturer_code: 'ORIG',
        manufacturer_name: 'Origin Jumpworks'
    });

    allShips.push({
        ship_code: 'DRAK_Dragonfly_Pink',
        ship_name: 'Dragonfly Star Kitten',
        manufacturer_code: 'DRAK',
        manufacturer_name: 'Drake Interplanetary'
    });

    allShips.push({
        ship_code: 'ANVL_Lightning_F8C',
        ship_name: 'F8C Lightning',
        manufacturer_code: 'ANVL',
        manufacturer_name: 'Anvil Aerospace'
    });

    allShips.push({
        ship_code: 'GRIN_PTV',
        ship_name: 'PTV',
        manufacturer_code: 'GRIN',
        manufacturer_name: 'Greycat Industrial'
    });

    // Dedupe
    allShips = [...new Map(allShips.map(v => [v.ship_code, v])).values()];

    // Sort
    allShips = allShips.sort((left, right) => left.ship_code.replace(/_/g, ' ') < right.ship_code.replace(/_/g, ' ') + '  ' ? -1 : 1)
    
    return allShips;
}

$.ajax({url: 'https://robertsspaceindustries.com/ship-matrix/index', success: (payload) => { rsiShips = sanitizeShipMatrix(payload.data) }})
```
