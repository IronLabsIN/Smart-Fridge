# Smart Fridge: Local Edge-AI Inventory Tracker

A self-hosted system on NVIDIA Jetson Nano using XIAO ESP32S3 Sense (IMU for door events), USB webcam for captures, YOLOv8n (TensorRT-optimized) for object detection, SQLite for logging, and Flask web dashboard.

## Quick Start
1. **Hardware Setup**: See Hardware Wiring below.
2. **Jetson Setup**: Flash JetPack 4.6.4, clone repo, run `bash jetson-setup/install_deps.sh`.
3. **MCU Firmware**: Upload `mcu-firmware/SmartFridgeIMU.ino` to XIAO using Arduino IDE on host PC.
4. **Optimize Model**: `python3 jetson-setup/optimize_model.py`.
5. **DB Setup**: `python3 jetson-setup/setup_db.py`.
6. **Services**: Enable systemd services: `sudo cp systemd/*.service /etc/systemd/system/ && sudo systemctl enable smartfridge.service webapp.service && sudo systemctl start smartfridge.service webapp.service`.
7. **Access Dashboard**: http://<jetson-ip>:5000 (default Flask port).
8. **Test**: Run testing scripts.

## Hardware Wiring
- Mount XIAO on fridge door (IMU detects tilt). Connect via USB to Jetson (/dev/ttyACM0).
- USB Webcam inside fridge (/dev/video0).
- No soldering; power XIAO from Jetson USB.

See `hardware/BOM.md` and `hardware/wiring_diagram.txt` (ASCII diagram).

## Software Flow
- XIAO detects door open/close → Serial to Jetson.
- Jetson captures image → YOLO detects items → Log to SQLite.
- Dashboard: Live events, inventory history, manual edits.

## Settings/Config
- Edit `python-services/utils.py`: SERIAL_PORT='/dev/ttyACM0', DB_PATH='../db/smartfridge.db'.
- Webcam: cam_id=0 in utils.py.
- Thresholds: THRESHOLD=0.5 in MCU firmware; confidence=0.5 in detection.py.
- Food Classes: Customize CLASSES in detection.py (YOLO COCO: filter for 'bottle', 'apple', etc.).
- Logs: In `logs/` (auto-created).

## Troubleshooting
See `docs/troubleshooting.md`.

## Safety & Privacy
See `docs/safety_privacy.md`.

## License
MIT. For non-commercial use.
