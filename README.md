import networkx as nx
import matplotlib.pyplot as plt

class AcronymPredictor:
    def __init__(self):
        self.data = {}

    def add_acronym_data(self, acronym, topic, predicted_outcome, confidence):
        if not acronym:
            raise ValueError("Acronym cannot be empty")
        self.data[acronym] = {
            "topic": topic,
            "predicted_outcome": predicted_outcome,
            "confidence": confidence,
            "actual_outcome": None
        }

    def update_outcome(self, acronym, actual_outcome, confidence):
        if acronym in self.data:
            self.data[acronym]["actual_outcome"] = actual_outcome
            self.data[acronym]["confidence"] = confidence
        else:
            raise KeyError(f"Acronym '{acronym}' not found.")

    def get_prediction(self, acronym):
        if acronym in self.data:
            return self.data[acronym]["predicted_outcome"], self.data[acronym]["confidence"]
        else:
            return None, None

    def calculate_accuracy(self, predicted, actual):
        if predicted == actual:
            return 1.0
        elif predicted and actual:
            words_predicted = set(predicted.split())
            words_actual = set(actual.split())
            common_words = words_predicted.intersection(words_actual)
            return len(common_words) / max(len(words_predicted), len(words_actual))
        else:
            return 0.0

class RecursiveAcronymModel:
    def __init__(self):
        self.acronym_predictor = AcronymPredictor()

    def simulate_evolution(self, acronym, initial_state, transformations, max_iterations=10):
        state = initial_state
        for i in range(max_iterations):
            predicted_outcome, confidence = self.acronym_predictor.get_prediction(acronym)

            new_state = self.apply_transformations(state, transformations)
            self.acronym_predictor.update_outcome(acronym, new_state, confidence)

            accuracy = self.acronym_predictor.calculate_accuracy(predicted_outcome, new_state)

            print(f"Iteration {i + 1}:")
            print(f"Current State: {state}")
            print(f"Predicted Outcome: {predicted_outcome}")
            print(f"New State: {new_state}")
            print(f"Accuracy: {accuracy}\n")

            state = new_state

    def apply_transformations(self, state, transformations):
        for transformation in transformations:
            state = transformation(state)
        return state

# Sample transformations
def phonetic_shift(state):
    return state.replace("cniht", "knight")

def semantic_shift(state):
    return state.replace("young man", "noble warrior")

# Instantiate model and predictor
model = RecursiveAcronymModel()

# Adding an acronym to track
model.acronym_predictor.add_acronym_data("knight", "Medieval Society", "A noble warrior", 0.9)

# Simulate the evolution of 'knight' through recursive transformations
model.simulate_evolution("knight", "cniht (young man)",
                         [phonetic_shift, semantic_shift], max_iterations=5)

# Example of visualizing with NetworkX (you'll need to install it if running locally)
transformations = {
    "S": ["-1 3", "SI"],
    "-1 3": ["S", "-1 2"],
    "-1 2": ["SIC", "-1 1"],
    "SIC": ["-1 3"],
    "SI": ["-1 2", "SIC"],
    "SI+X": ["-1 2 + X"]
}

G = nx.DiGraph()
for state, next_states in transformations.items():
    for next_state in next_states:
        G.add_edge(state, next_state)

pos = nx.spring_layout(G, seed=42)
nx.draw(G, pos, with_labels=True, node_size=3000, node_color="lightblue", font_size=10, font_weight="bold", arrowsize=20)
plt.title("JARIS ACRONYM Recursive Transformation Graph")
plt.show()

