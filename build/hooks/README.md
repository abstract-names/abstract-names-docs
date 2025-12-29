---
description: Discover the hooks available in the abstract-names-SDK package.
---

# Hooks

### Available Hooks

#### Resolution Hooks

* [**useResolve**](useresolve.md) - Resolve name to address
* [**useReverseResolve**](usereverseresolve.md) - Get primary name for an address
* [**useProfile**](useprofile.md) - Get complete profile data including text records

#### Validation Hooks

* [**useValidateName**](usevalidatename.md) - Contract-based name validation with Unicode support
* [**useValidateNameDebounced**](usevalidatenamedebounced.md) - Debounced validation for real-time input

#### Write Operation Hooks

* [**useSetTextRecord**](usesettextrecord.md) - Set individual text records
* [**useBatchSetText**](usebatchsettext.md) - Gas-efficient batch text record updates
* [**useSetPrimaryName**](usesetprimaryname.md) - Set or unset primary names
* [**useSetAddress**](usesetaddress.md) - Set resolved address for names

#### Pricing & Phase Hooks

* [**useNamePrice**](usenameprice.md) - Get tier-based pricing information
* [**useMintPhase**](usemintphase.md) - Get current mint phase

#### Utility Hooks

* [**useNameAvailability**](usenameavailability.md) - Check if a name is available for registration
* [**useNameExpiry**](usenameexpiry.md) - Get expiration info with days until expiry
* [**useTextRecord**](usetextrecord.md) - Fetch individual text records
* [**useAllowedTextKeys**](useallowedtextkeys.md) - Get supported text record keys
