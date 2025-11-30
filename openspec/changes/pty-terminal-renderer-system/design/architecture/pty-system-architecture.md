# PTY System Architecture

## Purpose
Defines the high-level architecture for the Enhanced PTY System with Graceful Degradation, showing component relationships and data flow.

## System Overview

The Enhanced PTY System provides Gemini CLI 0.19-level terminal interaction capabilities through a multi-layered architecture that ensures reliability and cross-platform compatibility.

## Architecture Diagram

```
┌─────────────────────────────────────────────────────────────────┐
│                    Enhanced PTY System Architecture                     │
├─────────────────────────────────────────────────────────────────┤
│                                                                     │
│  ┌─────────────┐  ┌─────────────┐  ┌─────────────────────────────────────────┐    │
│  │ Enhanced    │  │ PTY          │  │         Execution Service             │    │
│  │ Smart        │  │ Configuration│  │   (EnhancedPTYExecutionService)       │    │
│  │ PTY Selector  │  │ Manager     │  │                                     │    │
│  │             │  │             │  │  ┌─────────────────────────────┐    │
│  └─────────────┘  └─────────────┘  │  │  │ Platform-Specific         │    │
│           │               │       │  │  │ PTY Backends              │    │
│  ┌─────────────┐  └─────────────┘  │       │  │  │ (ConPTY, Terminado,      │    │
│  │ Terminal   │               │       │  │  │  │ ptyprocess, child_process) │    │
│  │ Renderer   │               │       │  │  └─────────────────────────────┘    │
│  │ (xterm     │               │       │  │                                     │    │
│  │ Emulation) │               │       │  │                                     │    │
│  └─────────────┘               │       │  │                                     │    │
│                             │       │  │                                     │    │
│  ┌─────────────────────────────┐ │       │  ┌─────────────────────────────┐    │
│  │      Event System Integration │       │  │      Legacy PTY Manager      │    │
│  │  (AG-UI Protocol Enhancements)    │       │  │      (Enhanced Version)       │    │
│  └─────────────────────────────┘       │  └─────────────────────────────┘    │
│                                     │                                     │
│  ┌─────────────────────────────────────────────────────────────┐    │
│  │                 Configuration Integration                    │    │
│  │  (Environment Variables, Config Files, User Settings)         │    │
│  └─────────────────────────────────────────────────────────────┘    │
└─────────────────────────────────────────────────────────────────┘
```

## Core Components

### 1. Enhanced Smart PTY Selector
**Purpose**: Intelligent PTY implementation selection with platform-specific optimization.

**Responsibilities**:
- Detect available PTY implementations
- Rank implementations by platform priority
- Provide capability assessment
- Cache availability status

### 2. PTY Configuration Manager
**Purpose**: Centralized configuration management for PTY behavior and preferences.

**Responsibilities**:
- Load and validate PTY configuration
- Provide configuration-driven decision making
- Support environment variable overrides
- Maintain backward compatibility

### 3. Enhanced PTY Execution Service
**Purpose**: Multi-layer execution with graceful degradation and error recovery.

**Responsibilities**:
- Execute commands with PTY when available
- Implement fallback mechanisms
- Provide event-driven status reporting
- Handle timeouts and interruptions

### 4. Platform-Specific PTY Backends
**Purpose**: Native PTY implementations for each platform.

**Implementations**:
- **ConPTY**: Windows 10+ native PTY
- **Terminado**: Cross-platform web terminal
- **ptyprocess**: Unix/Linux native PTY
- **child_process**: Traditional subprocess fallback

### 5. Terminal Renderer
**Purpose**: xterm-compatible terminal emulation for accurate output rendering.

**Features**:
- ANSI escape sequence processing
- Color and formatting preservation
- Interactive terminal features
- Screen buffer management

**Architecture Components**:

#### 5.1. Terminal State Manager
```python
class TerminalState:
    """终端状态管理器 - 对齐Gemini CLI的terminalSerializer"""
    def __init__(self, cols: int = 80, rows: int = 24):
        self.cols = cols
        self.rows = rows
        self.cursor_x = 0
        self.cursor_y = 0
        self.buffer = [[' ' for _ in range(cols)] for _ in range(rows)]
        self.attributes = [[None for _ in range(cols)] for _ in range(rows)]

    def resize(self, new_cols: int, new_rows: int):
        """调整终端尺寸"""
        # 实现缓冲区调整逻辑
        pass
```

#### 5.2. ANSI Parser
```python
class ANSIParser:
    """ANSI转义序列解析器 - 完全对齐Gemini CLI实现"""

    def __init__(self, terminal_state: TerminalState):
        self.terminal_state = terminal_state
        self.parser_state = {
            'in_escape': False,
            'escape_buffer': [],
            'current_attributes': ANSIAttributes()
        }

    def feed(self, data: bytes) -> None:
        """处理原始输出数据"""
        # 实现完整的ANSI序列解析
        # 支持颜色、光标控制、窗口操作等
        pass

    def parse_sgr(self, params: List[int]) -> ANSIAttributes:
        """解析Select Graphic Rendition序列"""
        # 实现SGR参数解析（颜色、格式等）
        pass
```

#### 5.3. Token Serializer
```python
@dataclass
class AnsiToken:
    """ANSI令牌 - 完全对齐Gemini CLI的AnsiToken接口"""
    text: str
    bold: bool = False
    italic: bool = False
    underline: bool = False
    dim: bool = False
    inverse: bool = False
    fg: str = ""  # 十六进制颜色
    bg: str = ""  # 十六进制颜色

@dataclass
class ANSIAttributes:
    """ANSI属性 - 对齐Gemini CLI的Cell属性"""
    bold: bool = False
    italic: bool = False
    underline: bool = False
    dim: bool = False
    inverse: bool = False
    fg_color: int = -1
    bg_color: int = -1
    fg_mode: ColorMode = ColorMode.DEFAULT
    bg_mode: ColorMode = ColorMode.DEFAULT

class TerminalSerializer:
    """终端序列化器 - 对齐Gemini CLI的serializeTerminalToObject"""

    def __init__(self, terminal_state: TerminalState):
        self.terminal_state = terminal_state

    def serialize_to_tokens(self) -> List[List[AnsiToken]]:
        """将终端状态序列化为ANSI令牌"""
        # 实现与Gemini CLI相同的序列化逻辑
        # 支持令牌合并、属性优化等
        pass

    def render_to_string(self) -> str:
        """渲染为纯文本输出（用于简单显示）"""
        pass
```

#### 5.4. Color Management
```python
class ColorManager:
    """颜色管理器 - 对齐Gemini CLI的convertColorToHex"""

    # ANSI 256色调色板 - 完全对齐Gemini CLI
    ANSI_COLORS = [
        '#000000', '#800000', '#008000', '#808000', '#000080', '#800080',
        '#008080', '#c0c0c0', '#808080', '#ff0000', '#00ff00', '#ffff00',
        '#0000ff', '#ff00ff', '#00ffff', '#ffffff',
        # ... 完整的256色调色板
    ]

    @staticmethod
    def convert_color_to_hex(color: int, mode: ColorMode, default: str) -> str:
        """转换为十六进制颜色 - 完全对齐Gemini CLI实现"""
        if mode == ColorMode.RGB:
            r = (color >> 16) & 255
            g = (color >> 8) & 255
            b = color & 255
            return f'#{r:02x}{g:02x}{b:02x}'
        elif mode == ColorMode.PALETTE:
            return ColorManager.ANSI_COLORS[color] if color < len(ColorManager.ANSI_COLORS) else default
        return default

    @staticmethod
    def detect_color_mode(params: List[int]) -> Tuple[ColorMode, int]:
        """检测颜色模式和值"""
        # 实现颜色模式检测逻辑
        pass
```

#### 5.5. Binary Content Detector
```python
class BinaryDetector:
    """二进制内容检测器 - 对齐Gemini CLI的isBinary检测"""

    def __init__(self, sample_size: int = 1024, threshold: float = 0.3):
        self.sample_size = sample_size
        self.threshold = threshold

    def is_binary(self, data: bytes) -> bool:
        """检测是否为二进制内容"""
        if len(data) < self.sample_size:
            return False

        sample = data[:self.sample_size]
        null_bytes = sample.count(b'\x00')
        binary_ratio = null_bytes / len(sample)

        # 检查其他二进制指示符
        control_chars = sum(1 for b in sample if b < 32 and b not in (9, 10, 13))
        control_ratio = control_chars / len(sample)

        return binary_ratio > self.threshold or control_ratio > 0.1

    def should_skip_rendering(self, data: bytes) -> bool:
        """判断是否应该跳过终端渲染"""
        return self.is_binary(data)
```

#### 5.6. Output Buffer Manager
```python
class OutputBuffer:
    """输出缓冲区管理器 - 对齐Gemini CLI的缓冲区管理"""

    def __init__(self, max_size: int = 10 * 1024 * 1024):  # 10MB
        self.max_size = max_size
        self.buffer: List[str] = []
        self.current_size = 0
        self.truncated = False

    def append(self, content: str) -> Tuple[str, bool]:
        """追加内容并返回新内容和截断状态"""
        content_size = len(content.encode('utf-8'))

        if self.current_size + content_size > self.max_size:
            # 实现循环缓冲区或截断逻辑
            self._make_room(content_size)
            self.truncated = True

        self.buffer.append(content)
        self.current_size += content_size

        return content, self.truncated

    def get_content(self) -> str:
        """获取缓冲区内容"""
        return ''.join(self.buffer)

    def _make_room(self, needed_size: int):
        """为新内容腾出空间"""
        # 实现缓冲区清理逻辑
        pass
```

### 6. Smart Encoding Detector
**Purpose**: Intelligent character encoding detection and conversion.

**Capabilities**:
- Multi-layer encoding detection
- BOM marker identification
- Statistical encoding analysis
- Graceful encoding fallbacks

## Data Flow

### Command Execution Flow

1. **Configuration Check**: Determine if PTY should be used
2. **Implementation Selection**: Choose best available PTY implementation
3. **Execution Attempt**: Try execution with selected implementation
4. **Fallback Handling**: If failure, try next implementation or child_process
5. **Result Processing**: Process output through encoding detection and terminal rendering
6. **Event Reporting**: Send execution events through AG-UI protocol

### Configuration Flow

1. **Load Configuration**: Read from config files and environment variables
2. **Validate Settings**: Ensure configuration validity and compatibility
3. **Apply Preferences**: Use user preferences for PTY selection
4. **Runtime Updates**: Support hot configuration reloading

## Integration Points

### Existing System Integration
- **Launch Script**: PTY dependency checking and status reporting
- **Config System**: PTY configuration integration
- **Shell Tool**: Enhanced execution service integration
- **PTY Manager**: Legacy system enhancement
- **Event System**: AG-UI protocol extensions

### External Dependencies
- **terminado**: Web terminal support
- **chardet**: Character encoding detection
- **ptyprocess**: Unix/Linux PTY support
- **Windows ConPTY APIs**: Native Windows PTY support
- **pyte**: Python terminal emulation library (替代@xterm/headless)

## Terminal Renderer Implementation Guide

### Implementation Priority

#### Phase 1: Core Terminal Emulation (🔴 Critical)
```python
# src/langchain_cli_agent/pty/terminal_renderer.py
"""
终端渲染器实现 - 完全对齐Gemini CLI的@xterm/headless功能
使用pyte作为Python生态的xterm兼容实现
"""

import pyte
from typing import List, Tuple, Optional, Dict, Any
from dataclasses import dataclass
from enum import Enum

class ColorMode(Enum):
    """颜色模式 - 完全对齐Gemini CLI"""
    DEFAULT = 0
    PALETTE = 1
    RGB = 2

@dataclass
class AnsiToken:
    """ANSI令牌 - 完全对齐Gemini CLI的AnsiToken接口"""
    text: str
    bold: bool = False
    italic: bool = False
    underline: bool = False
    dim: bool = False
    inverse: bool = False
    fg: str = ""
    bg: str = ""

class TerminalRenderer:
    """终端渲染器 - 对齐Gemini CLI的终端序列化功能"""

    def __init__(self, cols: int = 80, rows: int = 24):
        self.screen = pyte.Screen(cols, rows)
        self.stream = pyte.Stream(self.screen)
        self.cols = cols
        self.rows = rows

    def feed(self, data: bytes) -> None:
        """处理PTY输出数据"""
        try:
            decoded_data = data.decode('utf-8', errors='replace')
            self.stream.feed(decoded_data)
        except UnicodeDecodeError:
            # 编码错误时的降级处理
            self.stream.feed(data.decode('latin-1', errors='replace'))

    def resize(self, cols: int, rows: int) -> None:
        """调整终端尺寸"""
        self.screen.resize(cols, rows)
        self.cols = cols
        self.rows = rows

    def serialize_to_tokens(self) -> List[List[AnsiToken]]:
        """序列化为ANSI令牌 - 对齐Gemini CLI的serializeTerminalToObject"""
        result = []

        for y in range(self.rows):
            line_tokens = []
            current_token = None
            current_text = ""

            for x in range(self.cols):
                char = self.screen.buffer[y][x]

                # 获取字符属性
                attrs = self._extract_attributes(char)

                # 检查是否需要创建新令牌
                if current_token and not self._attributes_equal(current_token, attrs):
                    # 结束当前令牌
                    line_tokens.append(AnsiToken(
                        text=current_text,
                        **current_token
                    ))
                    current_text = ""
                    current_token = None

                # 开始新令牌或继续当前令牌
                if not current_token:
                    current_token = attrs

                current_text += char.data

            # 添加最后一个令牌
            if current_text:
                line_tokens.append(AnsiToken(
                    text=current_text,
                    **current_token
                ))

            result.append(line_tokens)

        return result

    def render_to_string(self) -> str:
        """渲染为纯文本字符串"""
        return '\n'.join(''.join(char.data for char in line) for line in self.screen.buffer)

    def _extract_attributes(self, char) -> Dict[str, Any]:
        """提取字符属性"""
        return {
            'bold': char.bold,
            'italic': char.italics,
            'underline': char.underline,
            'dim': char.dim,
            'inverse': char.reverse,
            'fg': self._color_to_hex(char.fg, 'foreground'),
            'bg': self._color_to_hex(char.bg, 'background')
        }

    def _attributes_equal(self, token1: Dict[str, Any], token2: Dict[str, Any]) -> bool:
        """比较两个属性集合是否相等"""
        return all(token1.get(k) == token2.get(k) for k in token1.keys())

    def _color_to_hex(self, color, color_type: str) -> str:
        """转换为十六进制颜色"""
        # 实现颜色转换逻辑，对齐Gemini CLI
        if color == 'default':
            return ''
        return str(color)  # 简化实现
```

#### Phase 2: Enhanced PTY Integration (🟡 High Priority)
```python
# src/langchain_cli_agent/pty/enhanced_pty_backend.py
"""
增强PTY后端 - 集成终端渲染器的完整PTY实现
"""

from typing import Tuple, Optional
import time
import threading
from .terminal_renderer import TerminalRenderer
from .binary_detector import BinaryDetector
from .output_buffer import OutputBuffer

class EnhancedPTYBackend(PTYBackend):
    """增强PTY后端 - 完全集成终端渲染器"""

    def __init__(self, config: PTYConfiguration):
        super().__init__(config)
        self.terminal_renderer = TerminalRenderer(
            cols=config.platform.conpty_size[0],
            rows=config.platform.conpty_size[1]
        )
        self.binary_detector = BinaryDetector()
        self.output_buffer = OutputBuffer()

    def start(self, command: Union[str, List[str]], cwd: Optional[str] = None,
              env: Optional[Dict[str, str]] = None, shell: bool = False) -> bool:
        """启动PTY进程"""
        try:
            # 使用ptyprocess创建真正的PTY
            self.process = ptyprocess.PtyProcess.spawn(
                command if shell else command.split() if isinstance(command, str) else command,
                cwd=cwd,
                env=env,
                dimensions=(self.terminal_renderer.rows, self.terminal_renderer.cols)
            )

            # 启动输出处理线程
            self._start_output_thread()
            return True

        except Exception as e:
            return False

    def _start_output_thread(self):
        """启动输出处理线程"""
        def process_output():
            while self.process and self.process.isalive():
                try:
                    data = self.process.read(1024)
                    if data:
                        # 检查二进制内容
                        if self.binary_detector.should_skip_rendering(data):
                            # 直接输出二进制内容标记
                            self._emit_binary_warning(data)
                            continue

                        # 通过终端渲染器处理
                        self.terminal_renderer.feed(data)

                        # 获取渲染结果
                        tokens = self.terminal_renderer.serialize_to_tokens()
                        string_output = self.terminal_renderer.render_to_string()

                        # 更新输出缓冲区
                        content, truncated = self.output_buffer.append(string_output)

                        # 发送到事件系统
                        self._emit_output_event(tokens, content, truncated)

                except Exception:
                    break

        threading.Thread(target=process_output, daemon=True).start()

    def _emit_output_event(self, tokens: List[List[AnsiToken]], content: str, truncated: bool):
        """发送输出事件到AG-UI系统"""
        if self.event_callback:
            self.event_callback(ExecutionEventInfo(
                event_type=ExecutionEvent.STDOUT,
                timestamp=time.time(),
                data={
                    'tokens': [[token.__dict__ for token in line] for line in tokens],
                    'content': content,
                    'truncated': truncated,
                    'pty_implementation': 'ptyprocess',
                    'terminal_size': {
                        'cols': self.terminal_renderer.cols,
                        'rows': self.terminal_renderer.rows
                    }
                }
            ))
```

#### Phase 3: Integration with Existing PTY Manager (🟢 Medium Priority)
```python
# src/langchain_cli_agent/pty/manager.py (增强版本)
"""
增强PTY管理器 - 集成终端渲染器的完整管理功能
"""

class EnhancedPTYManager(PTYManager):
    """增强PTY管理器 - 完全集成终端渲染器"""

    def __init__(self, event_runner: EventStreamRunner):
        super().__init__(event_runner)
        self.terminal_renderers: Dict[str, TerminalRenderer] = {}

    def create_session_with_terminal(self, session_id: str, command: str,
                                   cols: int = 80, rows: int = 24) -> PTYSession:
        """创建带终端渲染器的PTY会话"""

        # 创建终端渲染器
        renderer = TerminalRenderer(cols, rows)
        self.terminal_renderers[session_id] = renderer

        # 创建PTY会话
        session = PTYSession(
            session_id=session_id,
            command=command,
            pty_size=PTYSize(cols=cols, rows=rows)
        )

        self.active_sessions[session_id] = session
        return session

    def process_pty_output(self, session_id: str, data: bytes) -> Dict[str, Any]:
        """处理PTY输出并返回渲染结果"""
        if session_id not in self.terminal_renderers:
            return {'error': 'Terminal renderer not found'}

        renderer = self.terminal_renderers[session_id]

        # 检查二进制内容
        binary_detector = BinaryDetector()
        if binary_detector.should_skip_rendering(data):
            return {
                'type': 'binary_content',
                'message': 'Binary content detected, skipping terminal rendering',
                'data_size': len(data)
            }

        # 通过终端渲染器处理
        renderer.feed(data)

        # 获取渲染结果
        tokens = renderer.serialize_to_tokens()
        string_output = renderer.render_to_string()

        return {
            'type': 'terminal_output',
            'tokens': [[token.__dict__ for token in line] for line in tokens],
            'content': string_output,
            'terminal_size': {
                'cols': renderer.cols,
                'rows': renderer.rows
            }
        }

    def resize_terminal(self, session_id: str, cols: int, rows: int) -> bool:
        """调整终端尺寸"""
        if session_id in self.terminal_renderers:
            self.terminal_renderers[session_id].resize(cols, rows)

            # 通知PTY进程调整尺寸
            if session_id in self.active_sessions:
                session = self.active_sessions[session_id]
                session.pty_size = PTYSize(cols=cols, rows=rows)
                # 发送尺寸调整信号到PTY进程

            return True
        return False
```

### Integration with Enhanced Execution Service

```python
# src/langchain_cli_agent/pty/enhanced_execution_service.py (集成版本)
"""
增强执行服务 - 完全集成终端渲染器的PTY执行
"""

class EnhancedPTYExecutionService:
    """增强PTY执行服务 - 集成终端渲染器"""

    def __init__(self, config: Optional[PTYConfiguration] = None,
                 event_callback: Optional[Callable[[ExecutionEventInfo], None]] = None):
        self.config = config or PTYConfiguration()
        self.event_callback = event_callback
        self.encoding_detector = EncodingDetector.create_for_cli()

        # 增强的PTY后端集成
        self.backend_classes = {
            "child_process": ChildProcessBackend,
        }

        if PTYPROCESS_AVAILABLE:
            self.backend_classes["ptyprocess_enhanced"] = EnhancedPTYBackend

        # 当前活动的后端
        self.current_backend: Optional[PTYBackend] = None
        self.current_implementation: Optional[str] = None

    def execute(self, request: ExecutionRequest) -> ExecutionResult:
        """执行命令，支持完整的终端渲染"""
        # ... 现有的执行逻辑 ...

        # 如果使用增强PTY后端，则通过终端渲染器处理输出
        if hasattr(self.current_backend, 'terminal_renderer'):
            # 终端渲染器已经集成在PTY后端中
            pass

        return result
```

## Performance Considerations

### Startup Optimization
- Lazy loading of PTY implementations
- Implementation availability caching
- Early validation and error handling

### Runtime Performance
- Minimal overhead for non-PTY execution
- Efficient event processing
- Optimized output buffering
- Memory-conscious session management

### Scalability
- Support for concurrent PTY sessions
- Resource pooling and reuse
- Efficient cleanup and garbage collection

## Error Handling Strategy

### Multi-Layer Error Handling
1. **Implementation Errors**: PTY library failures, platform incompatibilities
2. **Runtime Errors**: Process termination, output encoding issues
3. **System Errors**: Resource exhaustion, timeout scenarios

### Recovery Mechanisms
- Automatic implementation switching
- Graceful degradation to child_process
- User-friendly error reporting
- System stability preservation

## Security Considerations

### Process Isolation
- Separate PTY processes for security
- Controlled resource access
- Safe command execution environment

### Configuration Security
- Validated configuration parameters
- Secure default settings
- Protection against configuration injection

This architecture provides a robust foundation for implementing Gemini CLI-level PTY functionality while ensuring system stability and cross-platform compatibility.