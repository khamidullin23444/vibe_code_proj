# Hello World Web API

A simple web application built with C# and .NET that returns "Hello world" as a JSON object at the root endpoint.

## Requirements

- Orange Pi running Ubuntu Linux (ARM architecture)
- Internet connection for downloading .NET SDK

## Installing .NET on Linux (ARM Architecture)

.NET can be installed on Linux ARM devices like Orange Pi using the installation script provided by Microsoft. Here are the steps:

### Step 1: Update System Packages

First, update your package list and install necessary dependencies:

```bash
sudo apt-get update
sudo apt-get install -y curl libunwind8 gettext libssl-dev
```

### Step 2: Download and Run .NET Installation Script

Download the official .NET installation script:

```bash
curl -sSL https://dot.net/v1/dotnet-install.sh -o dotnet-install.sh
chmod +x dotnet-install.sh
```

### Step 3: Install .NET SDK

Install the latest stable version of .NET SDK:

```bash
./dotnet-install.sh --channel STS --architecture arm64
```

**Note:** For ARM architecture, use `--architecture arm64` or `--architecture arm` depending on your Orange Pi model. Most modern Orange Pi devices use ARM64.

### Step 4: Add .NET to PATH

Add .NET to your system PATH so you can use the `dotnet` command from anywhere:

```bash
echo 'export DOTNET_ROOT=$HOME/.dotnet' >> ~/.bashrc
echo 'export PATH=$PATH:$HOME/.dotnet:$HOME/.dotnet/tools' >> ~/.bashrc
source ~/.bashrc
```

### Step 5: Verify Installation

Verify that .NET is installed correctly:

```bash
dotnet --version
```

You should see the version number (e.g., 8.0.x) displayed.

### Alternative: Manual Installation

If the script method doesn't work, you can manually download and extract the .NET SDK:

1. Visit [.NET Downloads](https://dotnet.microsoft.com/download/dotnet)
2. Select the latest .NET version
3. Download the Linux ARM64 (or ARM32) build
4. Extract the archive:
   ```bash
   mkdir -p $HOME/.dotnet
   tar zxf dotnet-sdk-*.tar.gz -C $HOME/.dotnet
   ```
5. Add to PATH as shown in Step 4

## Running the Application

### Step 1: Navigate to Project Directory

```bash
cd /path/to/vibe_code_proj
```

### Step 2: Restore Dependencies

```bash
dotnet restore
```

### Step 3: Build the Application

```bash
dotnet build
```

### Step 4: Run the Application

```bash
dotnet run
```

The application will start and listen on `http://localhost:5000` (or `http://[::]:5000` for IPv6).

### Step 5: Test the Application

Open a browser or use `curl` to test the endpoint:

```bash
curl http://localhost:5000/
```

You should receive a JSON response:

```json
{"message":"Hello world"}
```

## Running as a Service (Optional)

To run the application as a systemd service on your Orange Pi:

### Step 1: Create Service File

Create a service file:

```bash
sudo nano /etc/systemd/system/helloworldapi.service
```

### Step 2: Add Service Configuration

Add the following content (adjust paths as needed):

```ini
[Unit]
Description=Hello World Web API
After=network.target

[Service]
Type=notify
User=your-username
WorkingDirectory=/path/to/vibe_code_proj
ExecStart=/home/your-username/.dotnet/dotnet /path/to/vibe_code_proj/HelloWorldApi.dll
Restart=always
RestartSec=10

[Install]
WantedBy=multi-user.target
```

### Step 3: Enable and Start Service

```bash
sudo systemctl daemon-reload
sudo systemctl enable helloworldapi.service
sudo systemctl start helloworldapi.service
```

### Step 4: Check Service Status

```bash
sudo systemctl status helloworldapi.service
```

## Publishing for Production

To create a self-contained deployment:

```bash
dotnet publish -c Release -r linux-arm64 --self-contained true
```

The published files will be in `bin/Release/net8.0/linux-arm64/publish/`.

## Troubleshooting

### Issue: `dotnet: command not found`

**Solution:** Make sure you've added .NET to your PATH and sourced your `.bashrc` file:
```bash
source ~/.bashrc
```

### Issue: Architecture mismatch errors

**Solution:** Ensure you're using the correct architecture flag. For ARM64 devices:
```bash
./dotnet-install.sh --channel STS --architecture arm64
```

For ARM32 devices:
```bash
./dotnet-install.sh --channel STS --architecture arm
```

### Issue: Port already in use

**Solution:** Change the port in `Program.cs` or set the `ASPNETCORE_URLS` environment variable:
```bash
export ASPNETCORE_URLS="http://localhost:5001"
dotnet run
```

## Additional Resources

- [.NET Documentation](https://learn.microsoft.com/dotnet/)
- [Deploying .NET apps to ARM single-board computers](https://learn.microsoft.com/dotnet/iot/deployment)
- [.NET Download Page](https://dotnet.microsoft.com/download)

## License

This project is provided as-is for educational purposes.

