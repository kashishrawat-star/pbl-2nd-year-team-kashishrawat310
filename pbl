// ============================================================
//  CYBER-ATTACK SIMULATION & NETWORK DEFENSE ENGINE v2.0
//  3rd Semester PBL - Data Structures & C++ OOP
//
//  Phase 1: OOP Architecture (Inheritance, Polymorphism, Abstraction)
//  Phase 2: File I/O (CSV Loader, After-Action Report), ANSI Colors
//  Phase 3: Interactive Engine Loop, Quarantine Defense System
// ============================================================

#include <iostream>
#include <vector>
#include <queue>
#include <unordered_map>
#include <limits>
#include <fstream>
#include <sstream>
#include <string>
#include <iomanip>
#include <ctime>
#include <algorithm>
#include <memory>

#ifdef _WIN32
#include <windows.h>
#endif

using namespace std;

// ==================== ANSI COLOR CODES ====================
// Phase 2: Color-coded terminal output for visual clarity.
// Red = Compromised, Green = Secured, Yellow = Quarantine/Warning.

namespace Color {
    const string RESET   = "\033[0m";
    const string RED     = "\033[31m";     // Compromised / Danger
    const string GREEN   = "\033[32m";     // Secured / Safe
    const string YELLOW  = "\033[33m";     // Warning / Quarantine
    const string BLUE    = "\033[34m";     // Information
    const string MAGENTA = "\033[35m";     // Malware / Attack
    const string CYAN    = "\033[36m";     // System / Engine
    const string BOLD    = "\033[1m";      // Bold text
    const string DIM     = "\033[2m";      // Dimmed text
}

// ============================================================
//  PHASE 1: OOP ARCHITECTURE
// ============================================================

// === KASHISH RAWAT'S CODE : OOP NODE ARCHITECTURE ===
// Role: Encapsulates the state, security clearance, and properties of 
// each individual computer/router in the simulated network.
class NetworkNode {
private:
    int id;
    string name;
    int clearanceLevel;    // Security clearance: 1 (Low) ... 5 (Maximum)
    bool compromised;      // Has this node been infected by malware?
    bool quarantined;      // Has this node been isolated by the defender?

public:
    NetworkNode()
        : id(0), name("Unknown"), clearanceLevel(1),
          compromised(false), quarantined(false) {}

    NetworkNode(int id, const string& name, int clearance)
        : id(id), name(name), clearanceLevel(clearance),
          compromised(false), quarantined(false) {}

    // --- Getters ---
    int getId() const { return id; }
    string getName() const { return name; }
    int getClearance() const { return clearanceLevel; }
    bool isCompromised() const { return compromised; }
    bool isQuarantined() const { return quarantined; }

    // --- Setters ---
    void setCompromised(bool status) { compromised = status; }
    void setQuarantined(bool status) { quarantined = status; }

    // Returns a color-coded status tag for terminal display
    string getStatusString() const {
        if (quarantined) return Color::YELLOW + "[QUARANTINED]" + Color::RESET;
        if (compromised) return Color::RED    + "[COMPROMISED]" + Color::RESET;
        return Color::GREEN + "[  SECURED  ]" + Color::RESET;
    }
};

// === ZISHAN ALI'S CODE : GRAPH ADJACENCY LIST STRUCTURE ===
// Role: Represents a directed edge (connection) between nodes, 
// holding the latency cost needed for the pathfinding algorithms.
struct NetworkConnection {
    int targetRouterId;
    int baseCost;          // Original latency / traversal cost
    bool isQuarantined;    // If true, this link has been severed by defense

    NetworkConnection(int target, int cost)
        : targetRouterId(target), baseCost(cost), isQuarantined(false) {}
};

// --- AttackResult Struct ---
// Stores the outcome of a single attack simulation for the after-action report.
struct AttackResult {
    string malwareName;
    string malwareType;
    int originNode;
    int targetNode;                        // -1 if worm (targets everything)
    vector<int> compromisedNodes;
    vector<int> blockedNodes;              // Nodes that resisted the attack
    unordered_map<int, int> pathCosts;     // Dijkstra distances (for trojan)
    vector<pair<int, int>> spreadPath;     // Pairs showing infection direction
};

// ============================================================
//  ABSTRACT MALWARE BASE CLASS
//  Demonstrates: Abstraction, Inheritance, Polymorphism
// ============================================================

// === KASHISH RAWAT'S CODE : POLYMORPHISM & INHERITANCE ===
// Role: Provides a base template for all viruses. Enforces that any 
// derived malware class must implement its own execute() method.
class Malware {
protected:
    string name;
    int clearanceLevel;    // Sophistication level: higher = bypasses more security

public:
    Malware(const string& name, int clearance)
        : name(name), clearanceLevel(clearance) {}

    virtual ~Malware() = default; // Virtual destructor for proper polymorphic cleanup

    // Pure virtual function — MUST be overridden by every derived class
    virtual AttackResult execute(
        unordered_map<int, vector<NetworkConnection>>& graph,
        unordered_map<int, NetworkNode>& nodes,
        int startNode
    ) = 0;

    string getName() const { return name; }
    int getClearance() const { return clearanceLevel; }
    virtual string getType() const = 0; // Pure virtual
};

// ============================================================
//  WORM CLASS — BFS-Based Malware
//  Spreads indiscriminately to ALL reachable neighbors, layer by layer.
// ============================================================

class Worm : public Malware {
public:
    Worm(const string& name, int clearance)
        : Malware(name, clearance) {}

    string getType() const override { return "Worm (BFS)"; }

    AttackResult execute(
        unordered_map<int, vector<NetworkConnection>>& graph,
        unordered_map<int, NetworkNode>& nodes,
        int startNode
    ) override {

        AttackResult result;
        result.malwareName = name;
        result.malwareType = getType();
        result.originNode  = startNode;
        result.targetNode  = -1; // Worms target everything

        // === ZISHAN ALI'S CODE : BREADTH-FIRST SEARCH (BFS) ENGINE ===
        // Role: Uses a standard Queue to simulate a virus spreading blindly 
        // outward, hitting all immediate neighbors before moving deeper.
        queue<int> spreadQueue;
        unordered_map<int, bool> visited;

        // --- Pre-flight checks on the starting node ---
        if (nodes.count(startNode) == 0) {
            cout << Color::RED << "  [ERROR] Router " << startNode
                 << " does not exist in the network!" << Color::RESET << "\n";
            return result;
        }
        if (nodes[startNode].isQuarantined()) {
            cout << Color::YELLOW << "  [BLOCKED] Router " << startNode
                 << " is quarantined. Worm cannot deploy." << Color::RESET << "\n";
            result.blockedNodes.push_back(startNode);
            return result;
        }
        if (nodes[startNode].getClearance() > clearanceLevel) {
            cout << Color::GREEN << "  [BLOCKED] Router " << startNode
                 << " security (Level " << nodes[startNode].getClearance()
                 << ") blocked the worm (Level " << clearanceLevel << ")."
                 << Color::RESET << "\n";
            result.blockedNodes.push_back(startNode);
            return result;
        }

        // --- Patient Zero: infect the starting node ---
        spreadQueue.push(startNode);
        visited[startNode] = true;
        nodes[startNode].setCompromised(true);
        result.compromisedNodes.push_back(startNode);

        cout << Color::MAGENTA << Color::BOLD
             << "\n  +============================================+\n"
             << "  |   WORM OUTBREAK: " << left << setw(25) << name << "|\n"
             << "  +============================================+"
             << Color::RESET << "\n";
        cout << Color::RED << "  [!] Patient Zero: Router " << startNode
             << " (" << nodes[startNode].getName() << ")" << Color::RESET << "\n";

        // --- BFS LOOP ---
        // The worm dequeues a compromised node and attempts to spread
        // to every directly connected neighbor, layer by layer.
        while (!spreadQueue.empty()) {
            int current = spreadQueue.front();
            spreadQueue.pop();

            for (const auto& conn : graph[current]) {
                int neighbor = conn.targetRouterId;
                if (visited[neighbor]) continue;
                visited[neighbor] = true;

                // ACCESS CONTROL CHECK 1: Is the link quarantined (severed)?
                if (conn.isQuarantined) {
                    cout << Color::YELLOW << "  [~] Link " << current
                         << " -> " << neighbor << " SEVERED. Spread blocked."
                         << Color::RESET << "\n";
                    result.blockedNodes.push_back(neighbor);
                    continue;
                }

                // ACCESS CONTROL CHECK 2: Is the target node itself quarantined?
                if (nodes[neighbor].isQuarantined()) {
                    cout << Color::YELLOW << "  [~] Router " << neighbor
                         << " (" << nodes[neighbor].getName()
                         << ") is QUARANTINED. Worm blocked."
                         << Color::RESET << "\n";
                    result.blockedNodes.push_back(neighbor);
                    continue;
                }

                // ACCESS CONTROL CHECK 3: Does the worm have sufficient clearance?
                if (nodes[neighbor].getClearance() > clearanceLevel) {
                    cout << Color::GREEN << "  [+] Router " << neighbor
                         << " (" << nodes[neighbor].getName()
                         << ") DEFENDED. (Security L"
                         << nodes[neighbor].getClearance()
                         << " > Worm L" << clearanceLevel << ")"
                         << Color::RESET << "\n";
                    result.blockedNodes.push_back(neighbor);
                    continue;
                }

                // All checks passed — node is compromised
                nodes[neighbor].setCompromised(true);
                result.compromisedNodes.push_back(neighbor);
                result.spreadPath.push_back({current, neighbor});
                spreadQueue.push(neighbor);

                cout << Color::RED << "  [!] Spread: Router " << current
                     << " -> Router " << neighbor
                     << " (" << nodes[neighbor].getName() << ")"
                     << Color::RESET << "\n";
            }
        }

        cout << Color::MAGENTA << "\n  [RESULT] "
             << result.compromisedNodes.size() << " node(s) compromised, "
             << result.blockedNodes.size()     << " node(s) defended.\n"
             << Color::RESET << "\n";
        return result;
    }
};

// ============================================================
//  TARGETED TROJAN CLASS — Dijkstra-Based Malware
//  Calculates and follows the fastest/cheapest path to a
//  specific high-value target node.
// ============================================================

class TargetedTrojan : public Malware {
private:
    int targetNode;

public:
    TargetedTrojan(const string& name, int clearance, int target)
        : Malware(name, clearance), targetNode(target) {}

    string getType() const override { return "Targeted Trojan (Dijkstra)"; }

    AttackResult execute(
        unordered_map<int, vector<NetworkConnection>>& graph,
        unordered_map<int, NetworkNode>& nodes,
        int startNode
    ) override {

        AttackResult result;
        result.malwareName = name;
        result.malwareType = getType();
        result.originNode  = startNode;
        result.targetNode  = targetNode;

        // --- Pre-flight validation ---
        if (nodes.count(startNode) == 0 || nodes.count(targetNode) == 0) {
            cout << Color::RED << "  [ERROR] Invalid router ID(s)!"
                 << Color::RESET << "\n";
            return result;
        }
        if (nodes[startNode].isQuarantined()) {
            cout << Color::YELLOW << "  [BLOCKED] Entry point Router "
                 << startNode << " is quarantined. Trojan cannot deploy."
                 << Color::RESET << "\n";
            result.blockedNodes.push_back(startNode);
            return result;
        }
        if (nodes[startNode].getClearance() > clearanceLevel) {
            cout << Color::GREEN << "  [BLOCKED] Entry point Router "
                 << startNode << " security (Level "
                 << nodes[startNode].getClearance()
                 << ") blocked the trojan (Level " << clearanceLevel << ")."
                 << Color::RESET << "\n";
            result.blockedNodes.push_back(startNode);
            return result;
        }

        cout << Color::MAGENTA << Color::BOLD
             << "\n  +============================================+\n"
             << "  |   TROJAN DEPLOYED: " << left << setw(23) << name << "|\n"
             << "  +============================================+"
             << Color::RESET << "\n";
        cout << Color::CYAN << "  [i] Entry: Router " << startNode
             << " | Target: Router " << targetNode << Color::RESET << "\n";

        // === ZISHAN ALI'S CODE : DIJKSTRA'S ALGORITHM ENGINE ===
        // Role: Uses a Min-Heap (priority_queue) to calculate the absolute 
        // lowest latency (fastest) attack path across weighted graph edges.
        unordered_map<int, int> fastestTime;
        unordered_map<int, int> previousNode; // For path reconstruction

        // Min-heap priority queue: {accumulated_cost, router_id}
        priority_queue<pair<int, int>,
                       vector<pair<int, int>>,
                       greater<pair<int, int>>> frontier;

        // Initialize all nodes as unreachable (infinity)
        for (const auto& p : nodes) {
            fastestTime[p.first]  = numeric_limits<int>::max();
            previousNode[p.first] = -1;
        }

        fastestTime[startNode] = 0;
        frontier.push({0, startNode});

        while (!frontier.empty()) {
            int currentTime   = frontier.top().first;
            int currentRouter = frontier.top().second;
            frontier.pop();

            // Skip if we already found a faster path to this node
            if (currentTime > fastestTime[currentRouter]) continue;

            // Early exit optimization: if we reached the target optimally
            if (currentRouter == targetNode) break;

            // Relax all outgoing edges from this node
            for (const auto& conn : graph[currentRouter]) {
                int next = conn.targetRouterId;
                int cost = conn.baseCost;

                // ACCESS CONTROL: Skip quarantined links
                if (conn.isQuarantined) continue;

                // ACCESS CONTROL: Skip quarantined nodes
                if (nodes[next].isQuarantined()) continue;

                // ACCESS CONTROL: Skip nodes the trojan cannot penetrate
                if (nodes[next].getClearance() > clearanceLevel) continue;

                int newTime = fastestTime[currentRouter] + cost;
                if (newTime < fastestTime[next]) {
                    fastestTime[next]  = newTime;
                    previousNode[next] = currentRouter;
                    frontier.push({newTime, next});
                }
            }
        }

        result.pathCosts = fastestTime;

        // --- Check if the target was reachable ---
        if (fastestTime[targetNode] == numeric_limits<int>::max()) {
            cout << Color::GREEN
                 << "\n  [+] Target Router " << targetNode
                 << " is UNREACHABLE. Attack FAILED!"
                 << Color::RESET << "\n\n";

            // Record all unreachable nodes as blocked
            for (const auto& p : nodes) {
                if (fastestTime[p.first] == numeric_limits<int>::max()) {
                    result.blockedNodes.push_back(p.first);
                }
            }
            return result;
        }

        // --- PATH RECONSTRUCTION: trace back from target to source ---
        vector<int> attackPath;
        int current = targetNode;
        while (current != -1) {
            attackPath.push_back(current);
            current = previousNode[current];
        }
        reverse(attackPath.begin(), attackPath.end());

        // Compromise every node along the shortest path
        cout << Color::RED << "\n  [!] Attack path found (total cost: "
             << fastestTime[targetNode] << " latency units):\n      ";
        for (size_t i = 0; i < attackPath.size(); i++) {
            int nodeId = attackPath[i];
            nodes[nodeId].setCompromised(true);
            result.compromisedNodes.push_back(nodeId);
            cout << nodeId;
            if (i < attackPath.size() - 1) {
                cout << " -> ";
                result.spreadPath.push_back({attackPath[i], attackPath[i + 1]});
            }
        }
        cout << Color::RESET << "\n";

        // --- Display full vulnerability analysis ---
        cout << Color::CYAN << "\n  [i] Full vulnerability analysis from Router "
             << startNode << ":" << Color::RESET << "\n";
        for (const auto& p : fastestTime) {
            string nodeName = nodes.count(p.first)
                              ? nodes[p.first].getName() : "Unknown";
            if (p.second == numeric_limits<int>::max()) {
                cout << Color::GREEN << "      Router " << p.first
                     << " (" << nodeName << "): SAFE (Unreachable)"
                     << Color::RESET << "\n";
                result.blockedNodes.push_back(p.first);
            } else {
                string clr = (p.second == 0) ? Color::CYAN : Color::YELLOW;
                cout << clr << "      Router " << p.first
                     << " (" << nodeName << "): "
                     << p.second << " latency units"
                     << Color::RESET << "\n";
            }
        }

        cout << Color::MAGENTA << "\n  [RESULT] "
             << result.compromisedNodes.size()
             << " node(s) compromised along the attack path.\n"
             << Color::RESET << "\n";
        return result;
    }
};

// ============================================================
//  NETWORK DEFENSE ENGINE
//  The central simulation manager that owns the graph,
//  coordinates attacks, and provides defense mechanisms.
// ============================================================

class NetworkDefenseEngine {
private:
    unordered_map<int, vector<NetworkConnection>> networkGraph;
    unordered_map<int, NetworkNode> networkNodes;
    vector<AttackResult> attackLog; // History for after-action reporting

public:
    // --- Node & Edge Management ---

    void addNode(int id, const string& name, int clearance) {
        networkNodes[id] = NetworkNode(id, name, clearance);
        if (networkGraph.find(id) == networkGraph.end()) {
            networkGraph[id] = vector<NetworkConnection>();
        }
    }

    void addConnection(int routerA, int routerB, int cost) {
        networkGraph[routerA].push_back(NetworkConnection(routerB, cost));
        networkGraph[routerB].push_back(NetworkConnection(routerA, cost));
    }

    bool nodeExists(int id) const {
        return networkNodes.count(id) > 0;
    }

    // =========================================================
    //  PHASE 2: FILE I/O
    // =========================================================

    // === SHUBHAM SINGH'S CODE : CSV TOPOLOGY PARSER ===
    // Role: Handles <fstream> File I/O to dynamically parse and construct 
    // the graph topology from an external .csv file.
    bool loadTopologyFromCSV(const string& filename) {
        ifstream file(filename);
        if (!file.is_open()) {
       