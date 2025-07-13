# tapp

TDX TAPP - Trusted Application (C++)
A comprehensive C++ implementation of a Trusted Domain Extensions (TDX) application platform that provides secure Docker Compose measurement, TDX attestation, and Ethereum key derivation capabilities.

🏗️ Project Structure
tdx_tapp/
├── include/
│   ├── boost.hpp           # Boost library interface
│   └── key_tool.hpp        # Key tool library interface
├── rpc/
│   └── tapp_service.proto  # gRPC protocol definition
├── src/
│   ├── boost.cpp           # Boost library implementation
│   ├── key_tool.cpp        # Key tool library implementation
│   ├── cli.cpp             # CLI application
│   └── tapp.cpp            # gRPC server implementation
├── tests/                  # Unit tests (optional)
├── deps/                   # TDX attestation sources (optional)
├── cmake/
│   └── FindTDX.cmake       # TDX detection module
├── CMakeLists.txt          # Build configuration
├── build.sh                # Automated build script
└── README.md               # This file
🚀 Features
📦 Boost Library
Docker Compose Measurement: Calculate cryptographic hashes of Docker volumes
TDX RTMR Extension: Extend Runtime Measurement Registers with compose file hash and volumes hash
TDX Quote Generation: Generate and export TDX attestation quotes
Secure Volume Tracking: Recursively hash directories and files
🔐 Key Tool Library
Ethereum Key Derivation: Generate Ethereum keypairs from TDX reports
Secure Memory Management: Automatic cleanup of sensitive data
Address Generation: Derive Ethereum addresses using Keccak-256
Mock TDX Support: Development-friendly mock implementation
🖥️ CLI Interface
Unified Commands: Single binary for all operations
File I/O: Read/write compose files and quotes
Real-time Feedback: Progress indicators and colored output
🌐 gRPC Service
StartApp: Deploy applications with measurement
GetQuote: Generate TDX attestation quotes
GetPubkey: Retrieve Ethereum keys and addresses
Reflection Support: Service discovery capabilities
🛠️ Dependencies
Required
C++17 or later
CMake 3.16+
OpenSSL 1.1.0+ (for cryptographic operations)
Protobuf 3.x (for gRPC)
gRPC 1.x (for remote procedure calls)
Optional
TDX Attestation Library (tdx_attest) - For real TDX hardware
Google Test (for unit testing)
clang-format (for code formatting)
🔧 Quick Start
Automated Build (Recommended)
# Complete build with all dependencies
./build.sh

# Build with specific options
./build.sh --build-type Debug --enable-testing

# Check TDX support
./build.sh tdx-check
Manual Build
# Install dependencies
sudo apt-get install build-essential cmake pkg-config libssl-dev

# Compile gRPC from source
git clone --recurse-submodules -b v1.50.0 https://github.com/grpc/grpc
cd grpc && mkdir build && cd build
cmake -DgRPC_INSTALL=ON -DgRPC_BUILD_TESTS=OFF ..
make -j4 && sudo make install

# Build project
mkdir build && cd build
cmake ..
make -j$(nproc)
🧪 Build Script Usage
./build.sh [OPTIONS] [TARGETS]
Options
--build-type TYPE         # Release|Debug (default: Release)
--install-prefix DIR      # Installation path (default: /usr/local)
--enable-testing         # Enable unit testing
--force-tdx             # Force TDX support even without hardware
--grpc-version VER      # gRPC version (default: v1.50.0)
--help                  # Show help
Targets
deps                    # Install system dependencies
grpc                   # Compile and install gRPC from source
tdx-check              # Check TDX hardware and library support
configure              # Configure CMake build
build                  # Compile project
test                   # Run tests (requires --enable-testing)
format                 # Format code with clang-format
install                # Install to system
examples               # Run example programs
package                # Create distribution packages
all                    # Full build pipeline (default)
clean                  # Clean build directory
Examples
# Full build with defaults
./build.sh

# Install gRPC only
./build.sh grpc

# Debug build with tests
./build.sh --build-type Debug --enable-testing build test

# Force TDX support for testing
./build.sh --force-tdx configure build

# Custom gRPC version
./build.sh --grpc-version v1.54.0 grpc

# Clean and rebuild
./build.sh clean configure build
📖 Usage
CLI Tool
Boost Commands
# Start application in report data mode
./build/tapp_cli boost start_app docker-compose.yml report

# Start application in RTMR mode
./build/tapp_cli boost start_app docker-compose.yml rtmr 3

# Measure docker compose volumes only
./build/tapp_cli boost measure docker-compose.yml 3

# Generate TDX quote
./build/tapp_cli boost quote output.dat
Key Commands
# Show public key only
./build/tapp_cli key pubkey

# Show Ethereum address only
./build/tapp_cli key address

# Show both keys and address
./build/tapp_cli key all
gRPC Server
# Start server on default port (50051)
./build/tapp_server

# Start server on custom address
./build/tapp_server localhost:8080

# Show help
./build/tapp_server --help
Library Usage
Boost Library
#include "boost.hpp"

boost_lib::BoostLib boost;

// Start application with RTMR mode
auto result = boost.start_app(compose_content, boost_lib::AttestationMode::RTMR, 3);
if (result.status == boost_lib::ErrorCode::SUCCESS) {
    boost_lib::BoostLib::print_hex("Hash", result.volumes_hash);
}

// Start application with Report Data mode
auto result = boost.start_app(compose_content, boost_lib::AttestationMode::REPORT_DATA);

// Generate TDX quote with additional data
std::vector<uint8_t> additional_data = {0x01, 0x02, 0x03};
auto quote = boost.generate_quote(additional_data);
if (quote.status == boost_lib::ErrorCode::SUCCESS) {
    // Save quote.quote_data to file
}

// Check attestation mode and measurement status
auto mode = boost.get_attestation_mode();
auto app_id = boost.get_app_identifier();
bool has_measurement = boost.has_valid_measurement();
Key Tool Library
#include "key_tool.hpp"

key_tool::KeyToolLib key_tool;

// Get Ethereum keys
auto result = key_tool.get_pubkey_from_report();
if (result.status == key_tool::ErrorCode::SUCCESS) {
    std::cout << "Address: " << result.eth_address_hex << std::endl;
}
🔒 TDX Support
Hardware Detection
The build system automatically detects TDX support through:

CPU flags (lscpu | grep tdx_guest)
Device files (/dev/tdx_guest)
Kernel modules (lsmod | grep tdx)
Library Configuration
System Library: Uses installed libtdx_attest if available
Local Sources: Builds from deps/ directory if system library not found
Mock Implementation: Provides development-friendly mock when TDX unavailable
Status Messages
TDX Support: ENABLED (System Library) - Using system TDX library
TDX Support: ENABLED (Local Implementation) - Using deps/ sources
TDX Support: DISABLED (No hardware support) - No TDX hardware detected
TDX Support: DISABLED (No library/sources) - No TDX library or sources
🔒 Security Features
🛡️ Memory Protection
Secure Buffers: RAII wrappers for sensitive data
Automatic Cleanup: Private keys cleared from memory immediately
No Persistent Storage: Keys exist only transiently in TDX environment
🔐 Cryptographic Operations
HKDF Key Derivation: RFC 5869 compliant key derivation
secp256k1 Curve: Ethereum-compatible elliptic curve cryptography
SHA-256/Keccak-256: Industry-standard hash algorithms
🏗️ TDX Integration
RTMR Extension: Secure measurement of application state
Quote Generation: Hardware-backed attestation proofs
Mock Support: Development without TDX hardware
📚 API Reference
Boost Library
namespace boost_lib {
    enum class AttestationMode {
        REPORT_DATA,  // Store measurement in report data
        RTMR         // Store measurement in RTMR register
    };
    
    class BoostLib {
        // New interface with attestation mode selection
        StartAppResult start_app(const std::string& compose_content, 
                                AttestationMode mode, int rtmr_index = 3);
        
        // Backward compatibility interface
        StartAppResult start_app(const std::string& compose_content, int rtmr_index);
        
        QuoteResult generate_quote(const std::vector<uint8_t>& additional_report_data = {});
        
        std::vector<uint8_t> calculate_compose_volumes_hash(const std::string& compose_content);
        std::vector<uint8_t> calculate_directory_hash(const std::string& dir_path);
        
        // Measurement management
        bool has_valid_measurement() const;
        AttestationMode get_attestation_mode() const;
        std::string get_app_identifier() const;
        void clear_measurement();
    };
}
Key Tool Library
namespace key_tool {
    class KeyToolLib {
        PubkeyResult get_pubkey_from_report();
        std::vector<uint8_t> get_public_key_only();
        std::vector<uint8_t> get_address_only();
        static std::string format_address_hex(const std::vector<uint8_t>& address);
        static void print_hex(const std::string& label, const std::vector<uint8_t>& data);
    };
}
gRPC Services
service TappService {
    rpc StartApp(StartAppRequest) returns (StartAppResponse);
    rpc GetQuote(GetQuoteRequest) returns (GetQuoteResponse);
    rpc GetPubkey(GetPubkeyRequest) returns (GetPubkeyResponse);
}
🛠️ Development
Code Formatting
./build.sh format
Testing
./build.sh --enable-testing test
TDX Hardware Check
./build.sh tdx-check
⚠️ Important Notes
Private Key Security: Private keys are never stored persistently and only exist temporarily in memory within the TDX environment.

TDX Hardware: This implementation includes mock TDX support for development. For production use with real TDX hardware, install the TDX attestation library or provide sources in deps/ directory.

Ethereum Compatibility: Generated keys and addresses are fully compatible with Ethereum mainnet and testnets.

Memory Safety: All sensitive cryptographic material is automatically cleared from memory using secure cleanup functions.

gRPC Compilation: The build script compiles gRPC from source, which may take 10-30 minutes depending on your system.

📄 License
This project is provided as-is for educational and development purposes.

🤝 Contributing
Fork the repository
Create a feature branch
Make your changes
Add tests for new functionality
Run the test suite: ./build.sh --enable-testing test
Format your code: ./build.sh format
Submit a pull request
