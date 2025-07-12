# EPlus-LLM

<div align="center" style="line-height: 1;">

[![GitHub Stars](https://img.shields.io/github/stars/Gangjiang1/EPlus-LLM?style=social)](https://github.com/Gangjiang1/EPlus-LLM)
[![GitHub Forks](https://img.shields.io/github/forks/Gangjiang1/EPlus-LLM?style=social)](https://github.com/Gangjiang1/EPlus-LLM/network/members)
[![License](https://img.shields.io/badge/License-Apache%202.0-blue.svg)](https://opensource.org/licenses/Apache-2.0)

[English](./README.md) | [中文版](./README_zh.md)

</div>


**EPlus-LLM series, natural language for auto-building energy modeling via LLM**

**_EPlus-LLM [v1](https://huggingface.co/EPlus-LLM/EPlus-LLMv1)/[v2](https://huggingface.co/EPlus-LLM/EPlus-LLMv2) models along with quick-start code [![Open In Colab](https://colab.research.google.com/assets/colab-badge.svg)](https://colab.research.google.com/github/Gangjiang1/EPlus-LLM/blob/main/v2/EPlus-LLMv2_inference.ipynb) are open-sourced on [Huggingface](https://huggingface.co/EPlus-LLM)_**

<div align="center">
  <img src="/figs/graphic.png" alt="Illustration of EPlus-LLMv2 for Auto-building energy modeling" width="700"/>
</div>


## 🎉 News
- 📄 [2025/04/18] (update #4): The paper related to the EPlus-LLMv2 platform has been accepted for publication in _Automation in Construction_.
[Paper here](https://doi.org/10.1016/j.autcon.2025.106223).
- ⚡️ [2025/01/15] (update #3): We release EPlus-LLMv2, successfully addressing the challenge of auto-building energy modeling (ABEM) in complex scenarios. The new version of the platform supports a wide range of modeling scenarios encountered in real-world building applications, significantly enhancing its breadth and flexibility. Based on comprehensive datasets and a large-scale LLM, we integrate techniques such as LoRA, mixed precision training, and model quantification to reduce computational burden and achieve efficient fine-tuning (without compensating performance).
[Paper coming soon](https://doi.org/10.1016/j.apenergy.2024.123431).
- 📄 [2025/01/14] (update #2): Our paper on using prompt engineering to inform LLMs for automated building energy modeling has been accepted by _Energy_.
[Paper here](https://doi.org/10.1016/j.energy.2025.134548).
- 🔥 [2024/05/016] (update #1): We first successfully implement natural language-based auto-building modeling by fine-tuning a large language model (LLM). 
[Paper here](https://doi.org/10.1016/j.apenergy.2024.123431).

## 🚀 Key Features
- Scalability: Auto-generates complex EnergyPlus models, including varying geometries, materials, thermal zones, hourly schedules, and more.
- Accuracy & Efficiency: Achieves 100% modeling accuracy while reducing manual modeling time by over 98%.
- Interaction & Automation: A user-friendly human-AI interface for seamless model creation and customization.

<div align="center">
  <img src="/figs/v2_paltform.png" alt="Description" width="600"/>
  <p><em>A user-friendly human-AI interface for EPlus-LLMv2.</em></p>
</div>

- Flexible Design Scenarios:

  ✅ Geometry: square, L-, T-, U-, and hollow-square-shaped buildings  
  ✅ Roof types: flat, gable, hip – customizable attic/ridge height  
  ✅ Orientation & windows: custom WWR, window placement, facade-specific controls  
  ✅ Walls & materials: thermal properties, insulation types  
  ✅ Internal loads: lighting, equipment, occupancy, infiltration/ventilation, schedules, heating/cooling setpoints  
  ✅ Thermal zoning: configurable multi-zone layouts with core & perimeter zones  

<div align="center">
  <img src="/figs/v2_prompt-model.png" alt="Prompt-Model Description" width="600"/>
  <p><em>The relationship between the prompt and the model.</em></p>
</div>

## 🏗️ Target Users
This current platform is designed for engineers, architects, and researchers working in building performance, sustainability, and resilience. It is especially useful during early-stage conceptual design when modeling decisions have the greatest impact.
<div align="center">
  <img src="/figs/v2_example1.png" alt="Examples of EPlus-LLMv2" width="600"/>
  <p><em>EXample scenarios of EPlus-LLMv2.</em></p>
</div>

## 🚀 Quick Start

Here provides a code snippet to show you how to load the EPlus-LLM and auto-generate building energy models.  
  
[![Open In Colab](https://colab.research.google.com/assets/colab-badge.svg)](https://colab.research.google.com/github/Gangjiang1/EPlus-LLM/blob/main/v2/EPlus-LLMv2_inference.ipynb)

```python
# ⚠️ Please make sure you have adequate GPU memory.
# ⚠️ Please make sure your EnergyPlus version is 9.6 for successful running.
# ⚠️ Download the v2_nextpart.idf file from the EPlus-LLMv2 repo and place it in your current working directory.

# ! pip install -U bitsandbytes -q # pip this repo at your first run
from transformers import AutoModelForSeq2SeqLM, AutoTokenizer
import torch
from peft import PeftModel, PeftConfig

# Load the EPlus-LLMv2 config. 
peft_model_id = "EPlus-LLM/EPlus-LLMv2"
config = PeftConfig.from_pretrained(peft_model_id)

# Load the base LLM, flan-t5-xxl, and tokenizer
model = AutoModelForSeq2SeqLM.from_pretrained("google/flan-t5-xxl", load_in_8bit=True)
tokenizer = AutoTokenizer.from_pretrained("google/flan-t5-xxl")

# Load the Lora model
model = PeftModel.from_pretrained(model, peft_model_id)

# Generation config
generation_config = model.generation_config
generation_config.max_new_tokens = 5000
generation_config.temperature = 0.1
generation_config.top_p = 0.1
generation_config.num_return_sequences = 1
generation_config.pad_token_id = tokenizer.eos_token_id
generation_config.eos_token_id = tokenizer.eos_token_id

# Please provide your input here — a description of the desired building
# For more details, please refer to the paper: https://doi.org/10.1016/j.autcon.2025.106223
input=f"""
Simulate a U-shaped building that is 99.73 meters high, with a gable roof.
The horizontal segment is 732.31 meters long and 17.54 meters wide.
The left vertical segment is 256.31 meters long and 206.96 meters wide.
The right vertical segment is 431.54 meters long and 62 meters wide.
The roof ridge is 8.77 meters to the length side of the horizontal segment, and 128.16 meters, 215.77 meters to the width side of the vertical segments, respectively.
The attic height is 139.71 meters. The building orientation is 62 degrees to the north.
The building has 3 thermal zones with each segment as one thermal zone.
The window-to-wall ratio is 0.32. The window sill height is 33.91 meters, the window height is 65.82 meters, and the window jamb width is 0.01 meters.
The window U-factor is 6.36 W/m2K and the SHGC is 0.89.
The wall is made of wood, with a thickness of 0.48 meters and the wall insulation is RSI 1.6 m2K/W, U-factor 0.63 W/m2K.
The roof is made of metal, with a thickness of 0.09 meters and the roof insulation is RSI 5.4 m2K/W, U-factor 0.19 W/m2K.
The floor is made of concrete, covered with carpet. The ventilation rate is 2.32 ach. The infiltration rate is 0.55 ach.
The people density is 16.61 m2/person, the light density is 4.48 W/m2, and the electric equipment density is 22.63 W/m2.
Occupancy starts at 7:00 and ends at 18:00. The occupancy rate is 1. The unoccupancy rate is 0.3.
The heating setpoint is 21.54 Celsius in occupancy period and 15.86 Celsius in unoccupancy period.
The cooling setpoint is 22.6 Celsius in occupancy period and 26.72 Celsius in unoccupancy period.
"""

# EPlus-LLM generating...
input_ids = tokenizer(input, return_tensors="pt", truncation=False)
generated_ids = model.generate(input_ids = input_ids.input_ids,
                           attention_mask = input_ids.attention_mask,
                           generation_config = generation_config)
generated_output = tokenizer.decode(generated_ids[0], skip_special_tokens=True)

# Default thermal zones setting 
zone_1 = """ZoneHVAC:EquipmentConnections,Thermal Zone 1,Thermal Zone 1 Equipment,Thermal Zone 1 Ideal Loads Supply Inlet,,Thermal Zone 1 Zone Air Node,Thermal Zone 1 Return Outlet;
ZoneHVAC:EquipmentList,Thermal Zone 1 Equipment,SequentialLoad,ZoneHVAC:IdealLoadsAirSystem,Thermal Zone 1 Ideal Loads Air System,1,1,,;
ZoneHVAC:IdealLoadsAirSystem,Thermal Zone 1 Ideal Loads Air System,,Thermal Zone 1 Ideal Loads Supply Inlet,,,50,13,0.0156,0.0077,NoLimit,,,NoLimit,,,,,ConstantSensibleHeatRatio,0.7,None,,,None,NoEconomizer,None,0.7,0.65;
ZoneControl:Thermostat,Thermal Zone 1 Thermostat,Thermal Zone 1,Thermostat Schedule,ThermostatSetpoint:DualSetpoint,Thermostat Setpoint Dual Setpoint,,,,,,,0;
Sizing:Zone,Thermal Zone 1,SupplyAirTemperature,14,11.11,SupplyAirTemperature,40,11.11,0.0085,0.008,Ventilation,,,DesignDay,0,0.000762,0,0,DesignDay,0,0.002032,0.1415762,0.3,,No;"""
zone_2 = """ZoneHVAC:EquipmentConnections,Thermal Zone 2,Thermal Zone 2 Equipment,Thermal Zone 2 Ideal Loads Supply Inlet,,Thermal Zone 2 Zone Air Node,Thermal Zone 2 Return Outlet;
ZoneHVAC:EquipmentList,Thermal Zone 2 Equipment,SequentialLoad,ZoneHVAC:IdealLoadsAirSystem,Thermal Zone 2 Ideal Loads Air System,1,1,,;
ZoneHVAC:IdealLoadsAirSystem,Thermal Zone 2 Ideal Loads Air System,,Thermal Zone 2 Ideal Loads Supply Inlet,,,50,13,0.0156,0.0077,NoLimit,,,NoLimit,,,,,ConstantSensibleHeatRatio,0.7,None,,,None,NoEconomizer,None,0.7,0.65;
ZoneControl:Thermostat,Thermal Zone 2 Thermostat,Thermal Zone 2,Thermostat Schedule,ThermostatSetpoint:DualSetpoint,Thermostat Setpoint Dual Setpoint,,,,,,,0;
Sizing:Zone,Thermal Zone 2,SupplyAirTemperature,14,11.11,SupplyAirTemperature,40,11.11,0.0085,0.008,Ventilation,,,DesignDay,0,0.000762,0,0,DesignDay,0,0.002032,0.1415762,0.3,,No;"""
zone_3 = """ZoneHVAC:EquipmentConnections,Thermal Zone 3,Thermal Zone 3 Equipment,Thermal Zone 3 Ideal Loads Supply Inlet,,Thermal Zone 3 Zone Air Node,Thermal Zone 3 Return Outlet;
ZoneHVAC:EquipmentList,Thermal Zone 3 Equipment,SequentialLoad,ZoneHVAC:IdealLoadsAirSystem,Thermal Zone 3 Ideal Loads Air System,1,1,,;
ZoneHVAC:IdealLoadsAirSystem,Thermal Zone 3 Ideal Loads Air System,,Thermal Zone 3 Ideal Loads Supply Inlet,,,50,13,0.0156,0.0077,NoLimit,,,NoLimit,,,,,ConstantSensibleHeatRatio,0.7,None,,,None,NoEconomizer,None,0.7,0.65;
ZoneControl:Thermostat,Thermal Zone 3 Thermostat,Thermal Zone 3,Thermostat Schedule,ThermostatSetpoint:DualSetpoint,Thermostat Setpoint Dual Setpoint,,,,,,,0;
Sizing:Zone,Thermal Zone 3,SupplyAirTemperature,14,11.11,SupplyAirTemperature,40,11.11,0.0085,0.008,Ventilation,,,DesignDay,0,0.000762,0,0,DesignDay,0,0.002032,0.1415762,0.3,,No;"""
zone_4 = """ZoneHVAC:EquipmentConnections,Thermal Zone 4,Thermal Zone 4 Equipment,Thermal Zone 4 Ideal Loads Supply Inlet,,Thermal Zone 4 Zone Air Node,Thermal Zone 4 Return Outlet;
ZoneHVAC:EquipmentList,Thermal Zone 4 Equipment,SequentialLoad,ZoneHVAC:IdealLoadsAirSystem,Thermal Zone 4 Ideal Loads Air System,1,1,,;
ZoneHVAC:IdealLoadsAirSystem,Thermal Zone 4 Ideal Loads Air System,,Thermal Zone 4 Ideal Loads Supply Inlet,,,50,13,0.0156,0.0077,NoLimit,,,NoLimit,,,,,ConstantSensibleHeatRatio,0.7,None,,,None,NoEconomizer,None,0.7,0.65;
ZoneControl:Thermostat,Thermal Zone 4 Thermostat,Thermal Zone 4,Thermostat Schedule,ThermostatSetpoint:DualSetpoint,Thermostat Setpoint Dual Setpoint,,,,,,,0;
Sizing:Zone,Thermal Zone 4,SupplyAirTemperature,14,11.11,SupplyAirTemperature,40,11.11,0.0085,0.008,Ventilation,,,DesignDay,0,0.000762,0,0,DesignDay,0,0.002032,0.1415762,0.3,,No;"""
zone_5 = """ZoneHVAC:EquipmentConnections,Thermal Zone 5,Thermal Zone 5 Equipment,Thermal Zone 5 Ideal Loads Supply Inlet,,Thermal Zone 5 Zone Air Node,Thermal Zone 5 Return Outlet;
ZoneHVAC:EquipmentList,Thermal Zone 5 Equipment,SequentialLoad,ZoneHVAC:IdealLoadsAirSystem,Thermal Zone 5 Ideal Loads Air System,1,1,,;
ZoneHVAC:IdealLoadsAirSystem,Thermal Zone 5 Ideal Loads Air System,,Thermal Zone 5 Ideal Loads Supply Inlet,,,50,13,0.0156,0.0077,NoLimit,,,NoLimit,,,,,ConstantSensibleHeatRatio,0.7,None,,,None,NoEconomizer,None,0.7,0.65;
ZoneControl:Thermostat,Thermal Zone 5 Thermostat,Thermal Zone 5,Thermostat Schedule,ThermostatSetpoint:DualSetpoint,Thermostat Setpoint Dual Setpoint,,,,,,,0;
Sizing:Zone,Thermal Zone 5,SupplyAirTemperature,14,11.11,SupplyAirTemperature,40,11.11,0.0085,0.008,Ventilation,,,DesignDay,0,0.000762,0,0,DesignDay,0,0.002032,0.1415762,0.3,,No;"""
generated_output = generated_output.replace("Ideal Load System Setting for Thermal Zone 1;", zone_1)
generated_output = generated_output.replace("Ideal Load System Setting for Thermal Zone 2;", zone_2)
generated_output = generated_output.replace("Ideal Load System Setting for Thermal Zone 3;", zone_3)
generated_output = generated_output.replace("Ideal Load System Setting for Thermal Zone 4;", zone_4)
generated_output = generated_output.replace("Ideal Load System Setting for Thermal Zone 5;", zone_5)

# Load the rest port of IDF file.
file_path = "v2_nextpart.idf" # File is in the repo. Please download.
output_path = "v2_final.idf"

# Output the building energy model in IDF file
with open(file_path, 'r', encoding='utf-8') as file:
    nextpart = file.read()
final_text = nextpart + "\n\n" + generated_output
with open(output_path, 'w', encoding='utf-8') as f:
    f.write(final_text)
print(f"Building Energy Model Auto-Generated: {output_path}")
```

## 📝 Citation

If you find our work helpful, feel free to give us a cite.

```
@article{jiang2025EPlus-LLMv2,
  author    = {Gang Jiang and Jianli Chen},
  title     = {Efficient fine-tuning of large language models for automated building energy modeling in complex cases},
  journal   = {Automation in Construction},
  volume    = {175},
  pages     = {106223},
  year      = {2025},
  month     = {July},
  doi       = {https://doi.org/10.1016/j.autcon.2025.106223}}

@article{jiang2025prompting,
  author    = {Gang Jiang and Zhihao Ma and Liang Zhang and Jianli Chen},
  title     = {Prompt engineering to inform large language models in automated building energy modeling},
  journal   = {Energy},
  volume    = {316},
  pages     = {134548},
  year      = {2025},
  month     = {Feb},
  doi       = {https://doi.org/10.1016/j.energy.2025.134548}}

@article{jiang2025EPlus-LLM,
  author    = {Gang Jiang and Zhihao Ma and Liang Zhang and Jianli Chen},
  title     = {EPlus-LLM: A large language model-based computing platform for automated building energy modeling},
  journal   = {Applied Energy},
  volume    = {367},
  pages     = {123431},
  year      = {2024},
  month     = {Aug},
  doi       = {https://doi.org/10.1016/j.apenergy.2024.123431}}
```
