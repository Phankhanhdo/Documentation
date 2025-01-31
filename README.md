# Documentation
# Documentation
# Bus.py
class Bus:
    def __init__(self, name: str):
        self.name = name
        self.v = 0.0  # Default voltage is 0.0 V

    def set_bus_v(self, bus_v: float):
        self.v = bus_v

# Test Bus
if __name__ == "__main__":
    bus = Bus("A")
    print(f"Bus Name: {bus.name}, Initial Voltage: {bus.v} V")
    bus.set_bus_v(5.0)
    print(f"Updated Voltage: {bus.v} V")

# Resistor.py
class Resistor:
    def __init__(self, name: str, bus1: str, bus2: str, r: float):
        self.name = name
        self.bus1 = bus1
        self.bus2 = bus2
        self.r = r
        self.g = self.calc_g()

    def calc_g(self):
        return 1 / self.r if self.r != 0 else float('inf')

# Test Resistor
if __name__ == "__main__":
    resistor = Resistor("R1", "A", "B", 100)
    print(f"Resistor Name: {resistor.name}, Bus1: {resistor.bus1}, Bus2: {resistor.bus2}, Resistance: {resistor.r} Ω, Conductance: {resistor.g} S")

# Load.py
class Load:
    def __init__(self, name: str, bus1: str, p: float, v: float):
        self.name = name
        self.bus1 = bus1
        self.p = p
        self.v = v
        self.g = self.calc_g()

    def calc_g(self):
        return self.p / (self.v ** 2) if self.v != 0 else 0.0

# Test Load
if __name__ == "__main__":
    load = Load("L1", "B", 50, 220)
    print(f"Load Name: {load.name}, Bus: {load.bus1}, P: {load.p} W, Voltage: {load.v} V, Conductance: {load.g} S")

# Vsource.py
class Vsource:
    def __init__(self, name: str, bus1: str, v: float):
        self.name = name
        self.bus1 = bus1
        self.v = v

# Test Vsource
if __name__ == "__main__":
    vsource = Vsource("VS1", "A", 10)
    print(f"Voltage Source Name: {vsource.name}, Bus: {vsource.bus1}, Voltage: {vsource.v} V")


from Bus import Bus
from Resistor import Resistor
from Load import Load
from Vsource import Vsource

class Circuit:
    def __init__(self, name: str):
        self.name = name
        self.buses = {}
        self.resistors = {}
        self.loads = {}
        self.vsource = None
        self.i = 0.0

    def add_bus(self, bus_name: str):
        bus = Bus(bus_name)
        self.buses[bus_name] = bus

    def add_resistor_element(self, name: str, bus1: str, bus2: str, r: float):
        resistor = Resistor(name, bus1, bus2, r)
        self.resistors[name] = resistor

    def add_load_element(self, name: str, bus1: str, p: float, v: float):
        load = Load(name, bus1, p, v)
        self.loads[name] = load

    def add_vsource_element(self, name: str, bus1: str, v: float):
        self.vsource = Vsource(name, bus1, v)
        self.buses[bus1].set_bus_v(v)

    def set_i(self, i: float):
        self.i = i

    def print_nodal_voltage(self):
        for bus_name, bus in self.buses.items():
            print(f"Bus {bus_name}: Voltage = {bus.v} V")

    def print_circuit_current(self):
        print(f"Circuit Current: {self.i} A")

# Test Circuit
if __name__ == "__main__":
    circuit = Circuit("Test Circuit")
    circuit.add_bus("A")
    circuit.add_bus("B")
    circuit.add_vsource_element("VS1", "A", 10)
    circuit.add_resistor_element("R1", "A", "B", 100)
    circuit.add_load_element("L1", "B", 50, 220)
    circuit.set_i(0.1)
    circuit.print_nodal_voltage()
    circuit.print_circuit_current()


from Circuit import Circuit

class Solution:
    def __init__(self, circuit: Circuit):
        self.circuit = circuit

    def do_power_flow(self):
        # Calculate circuit current (I) using total conductance
        v_a = self.circuit.buses["A"].v  # Voltage at Bus A (Va)
        g_r = self.circuit.resistors["Rab"].g  # Conductance of resistor Rab
        g_l = self.circuit.loads["Lb"].g  # Conductance of load Lb

        self.circuit.i = v_a * (g_r * g_l / (g_r + g_l))  # Circuit current

        # Calculate voltage at Bus B (Vb)
        v_b = self.circuit.i / g_l
        self.circuit.buses["B"].set_bus_v(v_b)

    def print_results(self):
        self.circuit.print_nodal_voltage()
        self.circuit.print_circuit_current()

if __name__ == "__main__":
    # Define the circuit
    circuit = Circuit("Test Circuit")
    circuit.add_bus("A")
    circuit.add_bus("B")
    circuit.add_vsource_element("VS1", "A", 100)  # Voltage source at Bus A = 100V
    circuit.add_resistor_element("Rab", "A", "B", 5)  # Resistor between A and B = 5 Ohms
    circuit.add_load_element("Lb", "B", 2000, 100)  # Load at Bus B = 2000W at 100V

    # Solve the circuit
    solution = Solution(circuit)
    solution.do_power_flow()
    solution.print_results()

from Circuit import Circuit
from Solution import Solution

if __name__ == "__main__":
    # Define the circuit
    circuit = Circuit("Test Circuit")
    circuit.add_bus("A")
    circuit.add_bus("B")
    circuit.add_vsource_element("VS1", "A", 100)  # Voltage source at Bus A = 100V
    circuit.add_resistor_element("Rab", "A", "B", 5)  # Resistor between A and B = 5 Ohms
    circuit.add_load_element("Lb", "B", 2000, 100)  # Load at Bus B = 2000W at 100V

    # Solve the circuit
    solution = Solution(circuit)
    solution.do_power_flow()
    solution.print_results()

@startuml
class Circuit {
  +name: str
  +buses: dict[string, Bus]
  +resistors: list[Resistor]
  +loads: dict[string, Load]
  +vsource: Vsource


  +add_bus(bus: str)
  +add_resistor_element(name: str, bus1: str, bus2: str, r: float)
  +add_load_element(name: str, bus1: str, p: float, v: float)
  +add_vsource_element(name: str, bus1: str, v: float)
  +set_i(i: float)
  +print_nodal_voltage()
  +print_circuit_current()
}

class Bus {
  +name: str
  +v: float
  +set_bus_v(bus_v: float): void
}

class Resistor {
  +name: str
  +bus1: str
  +bus2: str
  +r: float
  +g: float
  +calc_g(): float
}

class Load {
  +name: str
  +bus1: str
  +p: float
  +v: float
  +r: float
  +g: float
  +calc_g(): float
}

class Vsource {
  +name: str
  +bus1: str
  +v: float
}

class Solution {
  +circuit: Circuit
  +do_power_flow()

}

Circuit "1" --> "many" Bus
Circuit "1" --> "many" Resistor
Circuit "1" --> "many" Load
Circuit "1" --> "1" Vsource
Solution --> Circuit
@enduml

