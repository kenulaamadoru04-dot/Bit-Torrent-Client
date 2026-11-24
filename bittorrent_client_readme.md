# PyTorrent: A Python BitTorrent Client

A lightweight, asynchronous BitTorrent client implemented from scratch in Python. This project demonstrates how the BitTorrent protocol works under the hood—including `.torrent` file bdecoding, tracker communication, peer-to-peer handshaking, piece management, and SHA-1 hash verification.

## Features

* **Bencoding Parser:** Custom encoder and decoder to parse `.torrent` metadata files and tracker responses.
* **Tracker Communication:** Supports HTTP/HTTPS trackers to announce client status and retrieve active peer lists.
* **Peer Wire Protocol:** Implements the TCP-based peer wire protocol (Handshake, Choke, Unchoke, Interested, Bitfield, Request, Piece).
* **Asynchronous Networking:** Built using Python's `asyncio` to handle concurrent connections with multiple peers efficiently.
* **Data Integrity:** Verifies every downloaded piece against the expected SHA-1 hashes defined in the torrent metadata.
* **Multi-File & Single-File Support:** Handles both single-file torrents and multi-file directory structures seamlessly.

## Prerequisites

* **Python 3.8+**
* **pip** (Python package manager)

*(Optional: If your implementation uses external libraries like `aiohttp` or `bitstring`, list them in a `requirements.txt` file).*

## Installation

1. **Clone the repository:**
   ```bash
   git clone https://github.com/yourusername/pytorrent.git
   cd pytorrent
   ```

2. **Create and activate a virtual environment (recommended):**
   ```bash
   python -m venv venv
   source venv/bin/activate  # On Windows use: venv\Scripts\activate
   ```

3. **Install dependencies:**
   ```bash
   pip install -r requirements.txt
   ```

## Usage

Run the client from the command line by passing the path to a `.torrent` file and an optional output directory:

```bash
python main.py path/to/file.torrent -o ./downloads
```

### Command-Line Arguments

| Argument | Short | Description | Default |
| :--- | :--- | :--- | :--- |
| `torrent_file` | — | Path to the `.torrent` file (Required) | — |
| `--output` | `-o` | Directory where downloaded files will be saved | `./downloads` |
| `--port` | `-p` | Port number to listen on for incoming peer connections | `6881` |
| `--max-peers` | `-m` | Maximum number of concurrent peer connections | `50` |

## Project Structure

```text
pytorrent/
├── main.py                 # Entry point and CLI argument parsing
├── torrent.py              # .torrent file parsing and metadata extraction
├── bencoding.py            # Bencode encoder and decoder implementation
├── tracker.py              # HTTP/UDP tracker communication
├── peer.py                 # Peer connection and Wire Protocol state machine
├── piece_manager.py        # Piece selection, block assembly, and SHA-1 checks
├── requirements.txt        # Project dependencies
└── README.md               # Project documentation
```

## How It Works

1. **Metadata Parsing:** Reads the `.torrent` file and decodes the Bencoded dictionary to extract the `announce` URL, piece length, piece hashes, and file layout.
2. **Tracker Announce:** Sends an HTTP `GET` request to the tracker containing the `info_hash` and a generated 20-byte `peer_id`. The tracker responds with a compact list of peer IP addresses and ports.
3. **Peer Handshake:** Initiates TCP connections with peers and exchanges the 68-byte BitTorrent handshake to verify the `info_hash`.
4. **Piece Exchange:** Sends an `Interested` message, waits to be `Unchoked`, and requests 16KB blocks of data.
5. **Verification & Disk I/O:** Once all blocks for a piece are received, the client computes its SHA-1 hash. If it matches the torrent metadata, the piece is written to disk.

## Roadmap & Future Improvements

* [ ] Add support for UDP trackers (BEP 15).
* [ ] Implement Rarest-First piece selection algorithm.
* [ ] Add DHT (Distributed Hash Table) support for trackerless torrents.
* [ ] Support Magnet links (BEP 9).
* [ ] Add a Terminal UI (TUI) using `rich` or `curses` to display real-time download speeds and progress bars.

## Disclaimer

This software was developed strictly for educational purposes to explore network programming and the BitTorrent protocol. Please ensure you have the legal right to download any content you access using this client.

## License

This project is licensed under the MIT License - see the [LICENSE](LICENSE) file for details.