# CppAutomationTools

Python scripts that generate C++ header and source files according to YAML config files.

Used to automate code generation in [this project](https://github.com/Anastasis575/DND-Character-Sheets).

## Auto enum
A script that produces a C++ enum class with a list of values, their names (string mappings), documentation comments and value access methods (get all values, get string of value).

The script can place the enum in a specified namespace and its implementation in a separate namespace (so as to hide implementation details).

[Example](#auto-enum-example)



## Auto constants
A script that produces a C++ header containing key-value pairs. Supports simple data types such as int, float, bool and std::string. The constants can be placed either on the global or on a specified namespace.

[Example](#auto-constants-example)

## How to run
To run any of the two scripts include the `generator.py` file alongside the script you want to execute. Then execute the script like this:
```bash
python <script file> <input file> <output directory> 
```
Where the input file must be a file following YAML syntax, and the output directory is the directory where the generated files will be placed.

## Examples

###   Auto enum example

enums.yml:
```yaml
Attribute: 
  details_namespace: entity_details
  namespace: DND
  type: enum
  documentation: |
    /**
    * @brief An enum describing the basic stats of a character.
    * @author Dimitris Tsirmpas
    */
  values:
    - Strength
    - Dexterity
    - Constitution
    - Intelligence
    - Wisdom
    - Charisma
```

Attribute.h:
```cpp
#pragma once
#include <unordered_map>

namespace DND {
	/**
	* @brief An enum describing the basic stats of a character.
	* @author Dimitris Tsirmpas
	*/
	enum class Attribute {
		Strength,
		Dexterity,
		Constitution,
		Intelligence,
		Wisdom,
		Charisma,
	};
	
	
	std::string attributeToString(DND::Attribute attribute);
	
	
	std::vector<Attribute> attributeValues();
	
	namespace entity_details {
		extern const std::unordered_map<Attribute, std::string> ATTRIBUTE_MAP;
	}
}
```

Attribute.cpp
```cpp
#include "Attribute.h"
#include <string>
#include <utility>
#include <algorithm>

namespace DND {
	
	std::string attributeToString(DND::Attribute attribute) {
		return entity_details::ATTRIBUTE_MAP.find(attribute)->second;
	}
	
	std::vector<Attribute> attributeValues() {
		std::vector<DND::Attribute> v;
		for each (auto it in entity_details::ATTRIBUTE_MAP) {
			v.push_back(it.first);
		}
		return v;
	}
	
	namespace entity_details {
		const std::unordered_map<Attribute, std::string> ATTRIBUTE_MAP = 
		 {
			std::make_pair(Attribute::Strength, "Strength"),
			std::make_pair(Attribute::Dexterity, "Dexterity"),
			std::make_pair(Attribute::Constitution, "Constitution"),
			std::make_pair(Attribute::Intelligence, "Intelligence"),
			std::make_pair(Attribute::Wisdom, "Wisdom"),
			std::make_pair(Attribute::Charisma, "Charisma"),
		};
	}
}
```


###  Auto constants example
rules.yml:
```yaml
Rules:
    type: constants
    namespace: DND::entity_details
    min_attribute_value : 0
    max_attribute_value : 20
    min_level : 1
    max_level : 20
    min_hp : 0
    max_hp : 9999
    min_ac : -5
    max_ac : 32
    min_speed : 1
    max_speed : 9999
    default_level : 1
    default_hp : 1
    default_ac : 0
    default_speed : 0
    extension: .chr 
```

Rules.h:
```cpp
#pragma once
#include <string>

namespace DND {
	namespace entity_details {
		const int MIN_ATTRIBUTE_VALUE = 0;
		const int MAX_ATTRIBUTE_VALUE = 20;
		const int MIN_LEVEL = 1;
		const int MAX_LEVEL = 20;
		const int MIN_HP = 0;
		const int MAX_HP = 9999;
		const int MIN_AC = -5;
		const int MAX_AC = 32;
		const int MIN_SPEED = 1;
		const int MAX_SPEED = 9999;
		const int DEFAULT_LEVEL = 1;
		const int DEFAULT_HP = 1;
		const int DEFAULT_AC = 0;
		const int DEFAULT_SPEED = 0;
		inline const std::string EXTENSION = ".chr";
	}
}
```
