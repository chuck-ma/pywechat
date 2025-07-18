# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

pywechat is a Windows-based PC WeChat automation tool built on pywinauto. It provides RPA (Robotic Process Automation) capabilities for WeChat operations through GUI automation. The project targets WeChat versions 3.9.12.xx and 4.0+ on Windows 10/11.

## Dependencies and Installation

### Core Dependencies (from requirements.txt and setup.py)
- pywinauto>=0.6.8 (Windows GUI automation framework)
- pyautogui>=0.9.54 (Mouse/keyboard automation)
- pywin32>=308 (Windows API access)
- psutil>=7.0.0 (Process management)
- pillow>=11.1.0 (Image processing)
- pycaw>=20240210 (Audio control)

### Installation Commands
```bash
# Install the package
pip install pywechat127==1.9.6

# Or upgrade
pip install --upgrade pywechat127

# Install development dependencies
pip install -r requirements.txt
```

## Architecture Overview

The project follows a modular architecture with clear separation of concerns:

### Core Modules Structure
```
pywechat/
├── WechatAuto.py      # Main automation functionality
├── WechatTools.py     # Utility tools and helpers
├── WinSettings.py     # Windows system settings
├── Uielements.py      # UI element definitions
├── Clock.py           # Timing and scheduling
├── Errors.py          # Custom exceptions
├── Warnings.py        # Warning definitions
└── __init__.py        # Package initialization
```

### Key Components

#### 1. WechatAuto Module
Contains the main automation classes:
- **Messages**: Send messages (single/multiple, individual/group)
- **Files**: File operations (send, save, forward)
- **FriendSettings**: Individual friend operations
- **GroupSettings**: Group chat operations
- **Contacts**: Contact management and retrieval
- **Call**: Voice/video calling
- **AutoReply**: Automated responses and call handling

#### 2. WechatTools Module
Provides utility functions:
- **Tools**: Core utilities for WeChat automation
- **API**: Opens specific WeChat mini-programs, official accounts, and video channels

#### 3. UI Element Management
- **Uielements**: Centralized UI element definitions with multi-language support
- Automatically detects WeChat language (Chinese Simplified, Traditional, English)
- Provides standardized UI element access patterns

## Development Guidelines

### Testing Environment
- All testing must be done in a virtual environment
- Use `source venv/bin/activate && python ...` for testing
- Or use `venv/bin/python3` directly

### Architecture Principles
- **Single-threaded**: WeChat doesn't support multi-threading; use sequential task execution
- **Resource sharing**: Set `close_wechat=False` in functions to avoid reopening WeChat for each operation
- **UI-based automation**: All operations work through GUI automation, not reverse engineering

### Common Usage Patterns

#### Basic Function Import
```python
# Direct function import
from pywechat import send_messages_to_friend

# Class-based import
from pywechat.WechatAuto import Messages
Messages.send_messages_to_friend()

# Module alias
import pywechat.WechatAuto as wechat
wechat.send_messages_to_friend()
```

#### Multi-task Operations
```python
# Efficient resource sharing
from pywechat.WechatAuto import Messages, Files
Messages.send_messages_to_friend(friend='友1', messages=['test'], close_wechat=False)
Files.send_files_to_friend(friend='友2', folder_path=r"C:\path", close_wechat=True)
```

## Key Features and Use Cases

### Real-time Message Monitoring
- **listen_on_chat()**: Monitor specific chat windows with automatic file/media saving
- **check_new_message()**: Monitor all conversations for new messages
- Both use polling-based message detection

### File Operations
- Automatic file saving from chat conversations
- Support for images, videos, documents
- Batch operations with folder-based input

### Automated Responses
- AI-powered auto-reply capabilities
- Group chat @-mention handling
- Automatic call answering with custom messages

## Windows-Specific Considerations

### Path Management
- WeChat path auto-detection through environment variables and registry
- Use `Tools.set_wechat_as_environ_path()` to add WeChat to PATH
- Registry keys: `HKEY_CURRENT_USER\Software\Tencent\WeChat\`

### UI Automation Constraints
- Requires WeChat window to be active and visible
- Dependent on specific WeChat versions (3.9.12.xx, 4.0+)
- Language-aware UI element detection

## Error Handling

The project includes comprehensive error handling through custom exceptions in `Errors.py`:
- Network connectivity issues
- Friend/group not found errors
- Permission and privacy errors
- File operation errors
- UI element detection failures

## Development Notes

### UI Element Access Pattern
```python
from pywechat.Uielements import Edits
from pywechat import Tools

# Get UI element definition
searchbar = Edits().SearchEdit
main_window = Tools.open_wechat()
main_window.child_window(**searchbar).type_keys('Hello!')
```

### Message Parsing Architecture
- Messages are retrieved through GUI list traversal
- Content parsing supports text, images, files, voice, video
- Polling-based real-time monitoring with configurable intervals

## Package Distribution

- PyPI package name: `pywechat127`
- Version: 1.9.6
- License: Apache-2.0
- Author: Hello-Mr-Crab

## Important Limitations

- Windows-only (Windows 10/11)
- Specific WeChat version dependencies
- GUI automation requires active window state
- No multi-threading support
- Performance limited by GUI automation speed