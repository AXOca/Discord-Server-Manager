# Discord Server Manager

> **<span style="color:red;">! WARNING: This tool may violate Discord's Terms of Service. Use it at your own risk. !</span>**

## Overview

This tool provides a user-friendly interface for seamlessly managing your Discord server list, especially for those who are members of numerous servers. I created this tool to help myself and others who need an efficient way to leave multiple servers at once. It includes features to mimic human behavior to reduce the likelihood of being flagged for automation, making the process more lenient and manageable.

## Features

- **Set Authorization Token:** Enter your Discord token to authenticate the tool.
- **Download Servers:** Save the IDs of servers you're a member of to a text file.
- **Import Whitelist:** Upload a text file containing a list of server IDs to whitelist.
- **Export Whitelist:** Download your current whitelist to a text file.
- **Whitelist Servers:** Search and select servers to add to your whitelist.
- **Leave Non-Whitelisted Servers:** Automatically leave servers that are not in your whitelist, with randomized delays to mimic human behavior.
- **Estimated Time:** Displays an estimated time to complete leaving non-whitelisted servers.

## How to Use

<details>
<summary>Click to expand instructions</summary>

1. **Open Discord in Your Browser:**
   - Log in to your Discord account.

2. **Open Developer Tools:**
   - Press `F12` or `Ctrl+Shift+I` (or `Cmd+Option+I` on Mac) to open the developer tools.

3. **Go to the Console Tab:**
   - Click on the "Console" tab in the developer tools.

4. **Paste the Script:**
   - Copy the entire script provided below and paste it into the console.

5. **Interact with the UI:**
   - A new UI will appear in the top-right corner of your screen with buttons for each feature.

</details>

## Script

<details>
<summary>Click to expand script</summary>

```javascript
(function() {
    function randomDelay(min, max) {
        return Math.floor(Math.random() * (max - min + 1)) + min;
    }

    function updateEstimatedTime(serversToLeaveCount) {
        const estimatedTotalTime = (serversToLeaveCount * (6000 + 12000) / 2) / 1000; // average delay
        const minutes = Math.floor(estimatedTotalTime / 60);
        const seconds = Math.floor(estimatedTotalTime % 60);
        statusBarEstimatedTime.textContent = `Estimated time to leave non-whitelisted servers: ${minutes}m ${seconds}s`;
    }

    function isUserOwnedServer(server) {
        return server.owner || server.permissions === '2147483647';
    }

    const container = document.createElement('div');
    container.style.position = 'fixed';
    container.style.top = '10px';
    container.style.right = '10px';
    container.style.backgroundColor = '#2c2f33';
    container.style.padding = '20px';
    container.style.borderRadius = '10px';
    container.style.zIndex = '1000';
    container.style.boxShadow = '0 4px 8px rgba(0, 0, 0, 0.3)';
    container.style.color = 'white';
    container.style.fontFamily = 'Arial, sans-serif';
    container.style.display = 'flex';
    container.style.flexDirection = 'column';
    container.style.alignItems = 'center';

    const closeButton = document.createElement('button');
    closeButton.textContent = 'Close';
    closeButton.style.position = 'absolute';
    closeButton.style.top = '1px';
    closeButton.style.right = '1px';
    closeButton.style.padding = '1px 2px';
    closeButton.style.borderRadius = '1px';
    closeButton.style.border = 'none';
    closeButton.style.backgroundColor = '#ff414d';
    closeButton.style.color = 'white';
    closeButton.style.cursor = 'pointer';
    closeButton.addEventListener('click', function() {
        document.body.removeChild(container);
    });

    const tokenInput = document.createElement('input');
    tokenInput.type = 'text';
    tokenInput.placeholder = 'Enter your token';
    tokenInput.style.marginBottom = '10px';
    tokenInput.style.padding = '5px';
    tokenInput.style.borderRadius = '5px';
    tokenInput.style.border = 'none';
    tokenInput.style.width = '200px';

    const setTokenButton = document.createElement('button');
    setTokenButton.textContent = 'Set Token';
    setTokenButton.style.marginBottom = '10px';
    setTokenButton.style.padding = '10px';
    setTokenButton.style.borderRadius = '5px';
    setTokenButton.style.border = 'none';
    setTokenButton.style.backgroundColor = '#7289da';
    setTokenButton.style.color = 'white';
    setTokenButton.style.cursor = 'pointer';

    const downloadButton = document.createElement('button');
    downloadButton.textContent = 'Download Servers';
    downloadButton.style.marginBottom = '10px';
    downloadButton.style.padding = '10px';
    downloadButton.style.borderRadius = '5px';
    downloadButton.style.border = 'none';
    downloadButton.style.backgroundColor = '#7289da';
    downloadButton.style.color = 'white';
    downloadButton.style.cursor = 'pointer';

    const importWhitelistButton = document.createElement('button');
    importWhitelistButton.textContent = 'Import Whitelist';
    importWhitelistButton.style.marginBottom = '10px';
    importWhitelistButton.style.padding = '10px';
    importWhitelistButton.style.borderRadius = '5px';
    importWhitelistButton.style.border = 'none';
    importWhitelistButton.style.backgroundColor = '#7289da';
    importWhitelistButton.style.color = 'white';
    importWhitelistButton.style.cursor = 'pointer';

    const exportWhitelistButton = document.createElement('button');
    exportWhitelistButton.textContent = 'Export Whitelist';
    exportWhitelistButton.style.marginBottom = '10px';
    exportWhitelistButton.style.padding = '10px';
    exportWhitelistButton.style.borderRadius = '5px';
    exportWhitelistButton.style.border = 'none';
    exportWhitelistButton.style.backgroundColor = '#7289da';
    exportWhitelistButton.style.color = 'white';
    exportWhitelistButton.style.cursor = 'pointer';

    const whitelistServersButton = document.createElement('button');
    whitelistServersButton.textContent = 'Whitelist Servers';
    whitelistServersButton.style.marginBottom = '10px';
    whitelistServersButton.style.padding = '10px';
    whitelistServersButton.style.borderRadius = '5px';
    whitelistServersButton.style.border = 'none';
    whitelistServersButton.style.backgroundColor = '#7289da';
    whitelistServersButton.style.color = 'white';
    whitelistServersButton.style.cursor = 'pointer';

    const leaveServersButton = document.createElement('button');
    leaveServersButton.textContent = 'Leave Non-Whitelisted Servers';
    leaveServersButton.style.marginBottom = '10px';
    leaveServersButton.style.padding = '10px';
    leaveServersButton.style.borderRadius = '5px';
    leaveServersButton.style.border = 'none';
    leaveServersButton.style.backgroundColor = '#7289da';
    leaveServersButton.style.color = 'white';
    leaveServersButton.style.cursor = 'pointer';

    const statusBar = document.createElement('div');
    statusBar.style.marginTop = '10px';
    statusBar.style.padding = '10px';
    statusBar.style.borderRadius = '5px';
    statusBar.style.backgroundColor = '#2c2f33';
    statusBar.style.color = 'white';
    statusBar.style.width = '100%';
    statusBar.style.textAlign = 'center';

    const statusBarEstimatedTime = document.createElement('div');
    statusBarEstimatedTime.style.marginTop = '10px';
    statusBarEstimatedTime.style.padding = '10px';
    statusBarEstimatedTime.style.borderRadius = '5px';
    statusBarEstimatedTime.style.backgroundColor = '#2c2f33';
    statusBarEstimatedTime.style.color = 'white';
    statusBarEstimatedTime.style.width = '100%';
    statusBarEstimatedTime.style.textAlign = 'center';

    container.appendChild(closeButton);
    container.appendChild(tokenInput);
    container.appendChild(setTokenButton);
    container.appendChild(downloadButton);
    container.appendChild(importWhitelistButton);
    container.appendChild(exportWhitelistButton);
    container.appendChild(whitelistServersButton);
    container.appendChild(leaveServersButton);
    container.appendChild(statusBar);
    container.appendChild(statusBarEstimatedTime);
    document.body.appendChild(container);

    let authToken = '';
    let whitelist = [];
    let serversList = [];

    setTokenButton.addEventListener('click', async () => {
        authToken = tokenInput.value;
        alert('Token set successfully!');

        if (!authToken) {
            alert('Please set your token first.');
            return;
        }

        try {
            const response = await fetch('/api/v9/users/@me/guilds', {
                headers: {
                    'Authorization': authToken
                }
            });

            if (!response.ok) {
                throw new Error('Failed to fetch servers');
            }

            serversList = await response.json();
            updateEstimatedTime(serversList.filter(server => !whitelist.includes(server.id)).length);
        } catch (error) {
            console.error('Error:', error);
            alert('Failed to fetch servers.');
        }
    });

    downloadButton.addEventListener('click', async () => {
        if (!authToken) {
            alert('Please set your token first.');
            return;
        }

        try {
            const response = await fetch('/api/v9/users/@me/guilds', {
                headers: {
                    'Authorization': authToken
                }
            });

            if (!response.ok) {
                throw new Error('Failed to fetch servers');
            }

            const servers = await response.json();
            const serversIds = servers.map(server => server.id);

            const blob = new Blob([serversIds.join('\n')], { type: 'text/plain' });
            const url = URL.createObjectURL(blob);

            const a = document.createElement('a');
            a.href = url;
            a.download = 'servers_ids.txt';
            document.body.appendChild(a);
            a.click();
            document.body.removeChild(a);

            alert('Server IDs saved to servers_ids.txt');
        } catch (error) {
            console.error('Error:', error);
            alert('Failed to download servers.');
        }
    });

    importWhitelistButton.addEventListener('click', () => {
        const input = document.createElement('input');
        input.type = 'file';
        input.accept = '.txt';

        input.addEventListener('change', (event) => {
            const file = event.target.files[0];
            const reader = new FileReader();

            reader.onload = (e) => {
                const importedIds = e.target.result.split('\n').map(id => id.trim()).filter(id => id);
                whitelist = whitelist.concat(importedIds.filter(id => !whitelist.includes(id)));
                alert('Whitelist imported successfully!');
                updateEstimatedTime(serversList.filter(server => !whitelist.includes(server.id)).length);
            };

            reader.readAsText(file);
        });

        input.click();
    });

    exportWhitelistButton.addEventListener('click', () => {
        const blob = new Blob([whitelist.join('\n')], { type: 'text/plain' });
        const url = URL.createObjectURL(blob);

        const a = document.createElement('a');
        a.href = url;
        a.download = 'Whitelist.txt';
        document.body.appendChild(a);
        a.click();
        document.body.removeChild(a);

        alert('Whitelist exported to Whitelist.txt');
    });

    whitelistServersButton.addEventListener('click', async () => {
        if (!authToken) {
            alert('Please set your token first.');
            return;
        }

        try {
            const response = await fetch('/api/v9/users/@me/guilds', {
                headers: {
                    'Authorization': authToken
                }
            });

            if (!response.ok) {
                throw new Error('Failed to fetch servers');
            }

            serversList = await response.json();
            serversList = serversList.filter(server => !isUserOwnedServer(server));
            serversList.sort((a, b) => a.joined_at - b.joined_at);

            const whitelistContainer = document.createElement('div');
            whitelistContainer.style.position = 'fixed';
            whitelistContainer.style.top = '10px';
            whitelistContainer.style.right = '250px';
            whitelistContainer.style.backgroundColor = '#2c2f33';
            whitelistContainer.style.padding = '20px';
            whitelistContainer.style.borderRadius = '10px';
            whitelistContainer.style.zIndex = '1000';
            whitelistContainer.style.boxShadow = '0 4px 8px rgba(0, 0, 0, 0.3)';
            whitelistContainer.style.color = 'white';
            whitelistContainer.style.fontFamily = 'Arial, sans-serif';
            whitelistContainer.style.maxHeight = '80%';
            whitelistContainer.style.overflowY = 'auto';

            const searchInput = document.createElement('input');
            searchInput.type = 'text';
            searchInput.placeholder = 'Search servers';
            searchInput.style.marginBottom = '10px';
            searchInput.style.padding = '5px';
            searchInput.style.borderRadius = '5px';
            searchInput.style.border = 'none';
            searchInput.style.width = '100%';

            searchInput.addEventListener('input', () => {
                const query = searchInput.value.toLowerCase();
                const serverItems = whitelistContainer.querySelectorAll('.server-item');
                serverItems.forEach(item => {
                    const serverName = item.querySelector('.server-name').textContent.toLowerCase();
                    if (serverName.includes(query)) {
                        item.style.display = '';
                    } else {
                        item.style.display = 'none';
                    }
                });
            });

            const closeBtn = document.createElement('button');
            closeBtn.textContent = 'Close';
            closeBtn.style.marginBottom = '10px';
            closeBtn.style.padding = '10px';
            closeBtn.style.borderRadius = '5px';
            closeBtn.style.border = 'none';
            closeBtn.style.backgroundColor = '#7289da';
            closeBtn.style.color = 'white';
            closeBtn.style.cursor = 'pointer';
            closeBtn.addEventListener('click', () => {
                document.body.removeChild(whitelistContainer);
            });

            whitelistContainer.appendChild(searchInput);
            whitelistContainer.appendChild(closeBtn);

            serversList.forEach(server => {
                const serverItem = document.createElement('div');
                serverItem.className = 'server-item';
                serverItem.style.display = 'flex';
                serverItem.style.alignItems = 'center';
                serverItem.style.marginBottom = '5px';

                const checkbox = document.createElement('input');
                checkbox.type = 'checkbox';
                checkbox.style.marginRight = '10px';
                checkbox.checked = whitelist.includes(server.id);

                checkbox.addEventListener('change', () => {
                    if (checkbox.checked) {
                        if (!whitelist.includes(server.id)) {
                            whitelist.push(server.id);
                        }
                    } else {
                        const index = whitelist.indexOf(server.id);
                        if (index > -1) {
                            whitelist.splice(index, 1);
                        }
                    }
                    updateEstimatedTime(serversList.filter(server => !whitelist.includes(server.id)).length);
                });

                const serverName = document.createElement('span');
                serverName.className = 'server-name';
                serverName.textContent = server.name;

                serverItem.appendChild(checkbox);
                serverItem.appendChild(serverName);
                whitelistContainer.appendChild(serverItem);
            });

            document.body.appendChild(whitelistContainer);
            updateEstimatedTime(serversList.filter(server => !whitelist.includes(server.id)).length);
        } catch (error) {
            console.error('Error:', error);
            alert('Failed to fetch servers.');
        }
    });

    leaveServersButton.addEventListener('click', async () => {
        if (!authToken) {
            alert('Please set your token first.');
            return;
        }
    
        try {
            const response = await fetch('/api/v9/users/@me/guilds', {
                headers: {
                    'Authorization': authToken
                }
            });
    
            if (!response.ok) {
                throw new Error('Failed to fetch servers');
            }
    
            const servers = await response.json();
            const serversToLeave = servers.filter(server => !whitelist.includes(server.id) && !isUserOwnedServer(server));
            const ownedServers = servers.filter(server => isUserOwnedServer(server));
    
            statusBar.textContent = `Found ${serversToLeave.length} servers to leave. Skipping ${ownedServers.length} owned servers.`;
            updateEstimatedTime(serversToLeave.length);
    
            const totalServersToLeave = serversToLeave.length;
            let leftServersCount = 0;
            let skippedServersCount = 0;
    
            for (let i = 0; i < serversToLeave.length; i++) {
                const server = serversToLeave[i];
    
                try {
                    const leaveResponse = await fetch(`/api/v9/users/@me/guilds/${server.id}`, {
                        method: 'DELETE',
                        headers: {
                            'Authorization': authToken
                        }
                    });
    
                    if (leaveResponse.status === 400) {
                        const errorData = await leaveResponse.json();
                        if (errorData.code === 50055) {
                            console.log(`Skipped server ${server.id} (Invalid Guild)`);
                            skippedServersCount++;
                            statusBar.textContent = `Left ${leftServersCount} servers, skipped ${skippedServersCount} (including ${ownedServers.length} owned). Processing ${i + 1} of ${serversToLeave.length}.`;
                        } else {
                            throw new Error(`Failed to leave server ${server.id}: ${errorData.message}`);
                        }
                    } else if (!leaveResponse.ok) {
                        throw new Error(`Failed to leave server ${server.id}: ${leaveResponse.statusText}`);
                    } else {
                        console.log(`Left server ${server.id}, waiting before next action...`);
                        leftServersCount++;
                        statusBar.textContent = `Left ${leftServersCount} servers, skipped ${skippedServersCount} (including ${ownedServers.length} owned). Processing ${i + 1} of ${serversToLeave.length}.`;
                    }
    
                    const delayTime = randomDelay(6000, 12000);
                    console.log(`Waiting for ${delayTime} ms before next request`);
                    await new Promise(resolve => setTimeout(resolve, delayTime));
    
                    const remainingServers = totalServersToLeave - leftServersCount - skippedServersCount;
                    const remainingTime = remainingServers * (delayTime / 1000);
                    const minutes = Math.floor(remainingTime / 60);
                    const seconds = Math.floor(remainingTime % 60);
                    statusBarEstimatedTime.textContent = `Estimated time remaining: ${minutes}m ${seconds}s`;
    
                } catch (error) {
                    console.error(`Error processing server ${server.id}:`, error);
                }
            }
    
            statusBar.textContent = `Completed. Left ${leftServersCount} servers, skipped ${skippedServersCount} (including ${ownedServers.length} owned servers).`;
            statusBarEstimatedTime.textContent = '';
        } catch (error) {
            console.error('Error:', error);
            alert('Failed to process servers: ' + error.message);
        }
    });
})();
```
</details>

## Buttons Explained

- **Set Token:**
  - Enter your Discord token in the text field and click "Set Token". This authenticates the tool with your Discord account.

- **Download Servers:**
  - Click this button to download a text file (`servers_ids.txt`) containing the IDs of all servers you're a member of.

- **Import Whitelist:**
  - Click this button and select a text file (`Whitelist.txt`) containing the IDs of servers you want to whitelist. The tool will update the estimated time based on the number of servers to leave.

- **Export Whitelist:**
  - Click this button to download your current whitelist to a text file (`Whitelist.txt`).

- **Whitelist Servers:**
  - Click this button to open a search and select interface where you can add or remove servers from your whitelist. The estimated time will update automatically as you modify the whitelist.

- **Leave Non-Whitelisted Servers:**
  - Click this button to start leaving servers that are not on your whitelist. The tool will display the number of servers being left and provide an estimated time for completion, which updates in real-time.

## Important Notes

- **Use Responsibly:** This tool is intended to help manage large server lists. However, it may violate Discord's Terms of Service, so use it at your own risk.
- **Avoid Automation Flags:** The tool includes delays between actions to mimic human behavior. However, there's no guarantee that this will prevent Discord from flagging your account for automation.

## Disclaimer

This tool is provided as-is, without any warranty or guarantee of any kind. The use of this tool is at your own risk, and the developers are not responsible for any consequences that may arise from its use, including but not limited to the banning or suspension of your Discord account.