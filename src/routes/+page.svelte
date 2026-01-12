<script lang="ts">
	// 1. Types remain the same
	type Task = {
		id: string;
		title: string;
		done: boolean;
	};

	type Note = {
		id: string;
		content: string;
		sourceUrl: string;
	};

	type Section = {
		id: string;
		title: string;
		description: string;
		tasks: Task[];
		notes: Note[];
	};

	const STORAGE_KEY = 'home_server_learning_plan_v2';

	const defaultSections: Section[] = [
		{
			id: 'big-picture',
			title: '1. Big picture & vocabulary',
			description:
				'Understand what a server is, what self-hosting means, and basic internet language.',
			tasks: [
				{ id: 'server-vs-laptop', title: 'Server vs normal laptop', done: false },
				{ id: 'self-hosting-meaning', title: 'What self-hosting actually means', done: false },
				{ id: 'ip-address', title: 'IP address (local vs public)', done: false },
				{ id: 'domain-dns', title: 'Domain names & DNS (high level)', done: false },
				{ id: 'ports-http-https', title: 'Ports, HTTP, and HTTPS basics', done: false }
			],
			notes: []
		},
		{
			id: 'linux',
			title: '2. Linux & Ubuntu basics',
			description: 'Get comfortable with Ubuntu and the command line on your server laptop.',
			tasks: [
				{ id: 'install-Ubuntu', title: 'Install Ubuntu on the old laptop', done: false },
				{
					id: 'terminal-basics',
					title: 'Terminal basics: ls, cd, cp, mv, rm, cat, nano',
					done: false
				},
				{ id: 'sudo-root', title: 'Understand sudo and root vs normal user', done: false },
				{ id: 'apt-install', title: 'Install software with apt (e.g., curl, git)', done: false },
				{ id: 'system-status', title: 'Check CPU, RAM, disk (top/htop, df -h)', done: false }
			],
			notes: []
		},
		{
			id: 'networking',
			title: '3. Networking fundamentals',
			description: 'Learn how your server talks to the world and how the world reaches it.',
			tasks: [
				{ id: 'local-vs-public-ip', title: 'Local IP vs public IP', done: false },
				{ id: 'router-nat', title: 'What your router does (NAT, home network)', done: false },
				{
					id: 'port-forwarding',
					title: 'Set up a simple port forward on your router',
					done: false
				},
				{ id: 'static-ip-lan', title: 'Give your server a static IP on your LAN', done: false },
				{
					id: 'dns-point-domain',
					title: 'Point a domain to your home IP (DNS A record)',
					done: false
				},
				{ id: 'dynamic-dns', title: 'Dynamic DNS (if your IP changes)', done: false }
			],
			notes: []
		},
		{
			id: 'docker',
			title: '4. Docker fundamentals',
			description: 'Use containers to run apps in an isolated, repeatable way.',
			tasks: [
				{ id: 'install-docker', title: 'Install Docker and test with hello-world', done: false },
				{
					id: 'images-vs-containers',
					title: 'Understand Docker images vs containers',
					done: false
				},
				{
					id: 'docker-run-ps-logs',
					title: 'Practice docker run / ps / logs / stop / rm',
					done: false
				},
				{ id: 'docker-volumes', title: 'Use a Docker volume for persistent data', done: false },
				{ id: 'docker-networks', title: 'Basic idea of Docker networks', done: false }
			],
			notes: []
		},
		{
			id: 'reverse-proxy',
			title: '5. Reverse proxy & HTTPS',
			description:
				'Put a smart “traffic director” in front of your apps and secure them with HTTPS.',
			tasks: [
				{
					id: 'reverse-proxy-idea',
					title: 'What a reverse proxy is and why you want one',
					done: false
				},
				{
					id: 'choose-proxy',
					title: 'Pick one: Caddy, Traefik, or Nginx (even if using Coolify)',
					done: false
				},
				{
					id: 'proxy-to-container',
					title: 'Route a domain to a test container via proxy',
					done: false
				},
				{ id: 'lets-encrypt', title: 'Get a Let’s Encrypt HTTPS certificate working', done: false },
				{ id: 'ports-80-443', title: 'Understand ports 80 and 443 in your setup', done: false }
			],
			notes: []
		},
		{
			id: 'coolify',
			title: '6. Coolify & app deployment',
			description:
				'Use Coolify as your control panel to deploy and manage projects with Docker underneath.',
			tasks: [
				{
					id: 'install-coolify',
					title: 'Install Coolify on your server (via Docker)',
					done: false
				},
				{
					id: 'coolify-dashboard',
					title: 'Explore the Coolify dashboard and settings',
					done: false
				},
				{ id: 'deploy-static-site', title: 'Deploy a simple static site via Coolify', done: false },
				{ id: 'connect-git', title: 'Connect a Git repository and deploy from it', done: false },
				{ id: 'env-vars', title: 'Use environment variables & secrets in Coolify', done: false },
				{ id: 'update-rollout', title: 'Update an app and roll out a new version', done: false }
			],
			notes: []
		},
		{
			id: 'operations',
			title: '7. Operations, security & backups',
			description: 'Make your setup safe, maintainable, and less likely to bite you later.',
			tasks: [
				{ id: 'ssh-keys', title: 'Set up SSH keys instead of password login', done: false },
				{
					id: 'ufw-firewall',
					title: 'Enable UFW firewall and allow only what you need',
					done: false
				},
				{ id: 'regular-updates', title: 'Update Ubuntu and Docker regularly', done: false },
				{
					id: 'backup-plan',
					title: 'Create a backup plan for important data/volumes',
					done: false
				},
				{ id: 'logs-monitoring', title: 'Know where logs live and how to check them', done: false }
			],
			notes: []
		},
		{
			id: 'Take a break',
			title: 'Break',
			description: 'Break',
			tasks: [{ id: 'chill', title: 'chill', done: false }],
			notes: []
		},
		{
			id: 'reachability-dns',
			title: '8. Public reachability & DNS',
			description: 'Make your home server reachable from the public internet in a controlled way.',
			tasks: [
				{
					id: 'choose-access-method',
					title: 'Decide access method: port forwarding vs secure tunnel',
					done: false
				},
				{
					id: 'router-port-forward',
					title: 'Configure router port forwarding (80/443) OR tunnel provider',
					done: false
				},
				{
					id: 'get-domain',
					title: 'Purchase or choose a domain name',
					done: false
				},
				{
					id: 'dns-a-record',
					title: 'Point domain DNS (A/AAAA record) to public IP or tunnel endpoint',
					done: false
				},
				{
					id: 'coolify-domain-bind',
					title: 'Bind domain to container using Coolify reverse proxy',
					done: false
				},
				{
					id: 'external-access-test',
					title: 'Confirm app loads externally via domain',
					done: false
				}
			],
			notes: []
		},
		{
			id: 'https-tls',
			title: '9. HTTPS & TLS fundamentals',
			description: 'Secure all public traffic with TLS and understand how certificates work.',
			tasks: [
				{
					id: 'enable-lets-encrypt',
					title: 'Enable Let’s Encrypt HTTPS in Coolify',
					done: false
				},
				{
					id: 'force-https',
					title: 'Force HTTP → HTTPS redirects',
					done: false
				},
				{
					id: 'cert-renewal-check',
					title: 'Verify certificate auto-renewal is working',
					done: false
				},
				{
					id: 'tls-understanding',
					title: 'Understand TLS certificates, chains, and renewals',
					done: false
				}
			],
			notes: []
		},
		{
			id: 'server-security',
			title: '10. Server security & hardening',
			description: 'Reduce attack surface and harden your Linux server for internet exposure.',
			tasks: [
				{
					id: 'non-root-user',
					title: 'Create and use a non-root Linux user',
					done: false
				},
				{
					id: 'ssh-keys-only',
					title: 'Disable SSH password login and use SSH keys only',
					done: false
				},
				{
					id: 'change-ssh-port',
					title: 'Change SSH port (optional hardening)',
					done: false
				},
				{
					id: 'enable-ufw',
					title: 'Enable UFW firewall',
					done: false
				},
				{
					id: 'ufw-rules',
					title: 'Allow only SSH, HTTP, and HTTPS in UFW',
					done: false
				},
				{
					id: 'auto-security-updates',
					title: 'Enable unattended security updates',
					done: false
				}
			],
			notes: []
		},
		{
			id: 'docker-production',
			title: '11. Docker for production',
			description: 'Understand how your application is built, run, and configured in Docker.',
			tasks: [
				{
					id: 'write-dockerfile',
					title: 'Write a Dockerfile for your Svelte app',
					done: false
				},
				{
					id: 'multi-stage-build',
					title: 'Use a multi-stage Docker build for production',
					done: false
				},
				{
					id: 'env-vars-docker',
					title: 'Understand environment variables in Docker',
					done: false
				},
				{
					id: 'volumes-vs-binds',
					title: 'Understand Docker volumes vs bind mounts',
					done: false
				},
				{
					id: 'manual-docker-run',
					title: 'Run the container manually without Coolify',
					done: false
				}
			],
			notes: []
		},
		{
			id: 'persistence-backups',
			title: '12. Persistence & backups',
			description: 'Protect your data so the system can be destroyed and rebuilt safely.',
			tasks: [
				{
					id: 'identify-state',
					title: 'Identify all stateful data (volumes, databases, config)',
					done: false
				},
				{
					id: 'backup-destination',
					title: 'Choose backup destination (NAS, another machine, cloud)',
					done: false
				},
				{
					id: 'automated-backups',
					title: 'Set up automated daily backups',
					done: false
				},
				{
					id: 'restore-test',
					title: 'Perform a test restore from backup',
					done: false
				}
			],
			notes: []
		},
		{
			id: 'observability',
			title: '13. Observability & monitoring',
			description: 'Know when something breaks and why it broke.',
			tasks: [
				{
					id: 'container-logs',
					title: 'Know how to access Docker and Coolify logs',
					done: false
				},
				{
					id: 'install-uptime-kuma',
					title: 'Deploy Uptime Kuma for service monitoring',
					done: false
				},
				{
					id: 'disk-memory-alerts',
					title: 'Set up disk and memory usage alerts',
					done: false
				},
				{
					id: 'journalctl-basics',
					title: 'Learn to read system logs with journalctl',
					done: false
				}
			],
			notes: []
		},
		{
			id: 'failure-drills',
			title: '15. Failure drills & resilience',
			description: 'Practice failure so outages do not cause panic.',
			tasks: [
				{
					id: 'simulate-outage',
					title: 'Simulate an outage and recover the service',
					done: false
				},
				{
					id: 'break-dns',
					title: 'Intentionally break DNS and fix it',
					done: false
				},
				{
					id: 'expire-cert',
					title: 'Simulate an expired TLS certificate',
					done: false
				},
				{
					id: 'disk-full',
					title: 'Fill disk space and observe system behavior',
					done: false
				}
			],
			notes: []
		}
	];

	// Initial load helper
	function getInitialData(): Section[] {
		if (typeof localStorage === 'undefined') return defaultSections;

		const stored = localStorage.getItem(STORAGE_KEY);
		if (!stored) return defaultSections;

		try {
			const parsed = JSON.parse(stored);
			if (!Array.isArray(parsed)) return defaultSections;
			return normalizeSections(parsed);
		} catch {
			return defaultSections;
		}
	}

	// 2. State Runes
	let sections = $state<Section[]>(getInitialData());
	let selectedSectionId = $state(sections[0]?.id ?? '');
	let newTaskTitle = $state('');

	// 3. Derived Runes (Replacement for $:)
	let selectedSection = $derived(sections.find((s) => s.id === selectedSectionId) ?? sections[0]);

	let totalTasks = $derived(sections.reduce((sum, s) => sum + s.tasks.length, 0));

	let completedTasks = $derived(
		sections.reduce((sum, s) => sum + s.tasks.filter((t) => t.done).length, 0)
	);

	let progressPercent = $derived(
		totalTasks === 0 ? 0 : Math.round((completedTasks / totalTasks) * 100)
	);

	// 4. Effect Rune (Replacement for $: saveToStorage)
	$effect(() => {
		localStorage.setItem(STORAGE_KEY, JSON.stringify(sections));
	});

	// 5. Logic functions
	function toggleTask(task: Task) {
		// In Svelte 5, we can mutate directly!
		task.done = !task.done;
	}

	function addTask(sectionId: string) {
		const title = newTaskTitle.trim();
		if (!title) return;

		const section = sections.find((s) => s.id === sectionId);
		if (section) {
			section.tasks.push({
				id: `${sectionId}-${Date.now()}`,
				title,
				done: false
			});
			newTaskTitle = '';
		}
	}

	function removeTask(section: Section, taskId: string) {
		section.tasks = section.tasks.filter((t) => t.id !== taskId);
	}

	function resetProgress() {
		if (!confirm('Reset all progress and custom tasks?')) return;
		sections = JSON.parse(JSON.stringify(defaultSections)); // Deep copy
		selectedSectionId = sections[0].id;
	}

	let newNoteContent = $state('');
	let newNoteSource = $state('');

	function addNote(section: Section) {
		const content = newNoteContent.trim();
		if (!content) return;

		section.notes.push({
			id: `note-${Date.now()}`,
			content,
			sourceUrl: newNoteSource.trim()
		});

		newNoteContent = '';
		newNoteSource = '';
	}

	function removeNote(section: Section, noteId: string) {
		section.notes = section.notes.filter((n) => n.id !== noteId);
	}
</script>

<div class="app">
	<header>
		<h1>Home Server Learning Plan</h1>
		<p class="subtitle">Work through the phases, check things off, and add your own tasks.</p>
	</header>

	<main>
		<aside class="sidebar">
			{#each sections as section (section.id)}
				<button
					class="section-button"
					class:active={section.id === selectedSectionId}
					onclick={() => (selectedSectionId = section.id)}
				>
					<span>{section.title}</span>
					<small>
						{section.tasks.filter((t) => t.done).length}/{section.tasks.length}
					</small>
				</button>
			{/each}

			<div class="progress-container">
				<div class="progress-label">
					<span>Overall progress</span>
					<span>{completedTasks}/{totalTasks} ({progressPercent}%)</span>
				</div>
				<div class="progress-bar-track">
					<div class="progress-bar-fill" style:width="{progressPercent}%"></div>
				</div>
			</div>

			<button class="reset-button" onclick={resetProgress}> Reset all progress </button>
		</aside>

		{#if selectedSection}
			<section class="section-detail">
				<h2>{selectedSection.title}</h2>
				<p>{selectedSection.description}</p>

				<div class="tasks">
					{#each selectedSection.tasks as task (task.id)}
						<div class="task" class:done={task.done}>
							<label>
								<input type="checkbox" checked={task.done} onchange={() => toggleTask(task)} />
								<span class="task-title" class:done={task.done}>
									{task.title}
								</span>
							</label>
							<button
								class="task-remove"
								onclick={() => removeTask(selectedSection!, task.id)}
								title="Remove task"
							>
								✕
							</button>
						</div>
					{/each}
				</div>

				<div class="add-task">
					<input
						type="text"
						placeholder="Add your own task to this section..."
						bind:value={newTaskTitle}
						onkeydown={(e) => e.key === 'Enter' && addTask(selectedSection!.id)}
					/>
					<button onclick={() => addTask(selectedSection!.id)}>Add</button>
				</div>

				<div class="hint">
					Tip: As you learn, rewrite tasks in your own words. When a line feels “obvious,” mark it
					done.
				</div>
			</section>

			<div class="notes-print-area">
				<div class="notes">
					<h3>Notes</h3>

					{#if selectedSection.notes.length === 0}
						<p class="empty-notes">No notes yet for this section.</p>
					{/if}

					{#each selectedSection.notes as note (note.id)}
						<div class="note">
							<p class="note-content">{note.content}</p>

							{#if note.sourceUrl}
								<a
									class="note-source"
									href={note.sourceUrl}
									target="_blank"
									rel="noopener noreferrer"
								>
									Source - {note.sourceUrl}
								</a>
							{/if}

							<button class="note-remove" onclick={() => removeNote(selectedSection!, note.id)}>
								Remove
							</button>
						</div>
					{/each}

					<div class="add-note">
						<textarea placeholder="Write your note here…" bind:value={newNoteContent}></textarea>

						<input
							type="url"
							placeholder="Optional source link (article, docs, video)"
							bind:value={newNoteSource}
						/>

						<button onclick={() => addNote(selectedSection!)}> Add note </button>
						<button class="print-notes" onclick={() => window.print()}> Print Notes </button>
					</div>
				</div>
			</div>
		{/if}
	</main>
</div>

<style>
	:global(body) {
		margin: 0;
		font-family:
			system-ui,
			-apple-system,
			BlinkMacSystemFont,
			'Segoe UI',
			sans-serif;
		background: #081605;
		color: #e5e7eb;
	}

	.app {
		min-height: 100vh;
		display: flex;
		flex-direction: column;
	}

	header {
		padding: 1.5rem 1.75rem;
		border-bottom: 1px solid rgba(148, 184, 151, 0.2);
		background: radial-gradient(circle at top left, #112a0f, #021702);
	}

	.subtitle {
		font-size: 0.9rem;
		color: #9ca3af;
	}

	main {
		display: grid;
		grid-template-columns: 260px 1fr;
		gap: 1rem;
		padding: 1rem 1.75rem 1.75rem;
	}

	@media (max-width: 800px) {
		main {
			grid-template-columns: 1fr;
		}
	}

	.sidebar {
		border-right: 1px solid rgba(148, 184, 153, 0.2);
		padding-right: 1rem;
	}

	@media (max-width: 800px) {
		.sidebar {
			border-right: none;
			border-bottom: 1px solid rgba(148, 184, 151, 0.2);
			padding-bottom: 1rem;
			margin-bottom: 1rem;
		}
	}

	.section-button {
		width: 100%;
		text-align: left;
		padding: 0.5rem 0.6rem;
		border-radius: 0.4rem;
		border: none;
		cursor: pointer;
		margin-bottom: 0.25rem;
		background: transparent;
		color: #e5e7eb;
		display: flex;
		justify-content: space-between;
		align-items: center;
		font-size: 0.9rem;
	}

	.section-button:hover {
		background: rgba(30, 175, 37, 0.4);
	}

	.section-button.active {
		background: linear-gradient(to right, #1dd826, #46e569);
	}

	.section-button small {
		font-size: 0.75rem;
		color: #ad9caf;
	}

	.section-button.active small {
		color: #ebe5eb;
	}

	.progress-container {
		margin-top: 0.75rem;
	}

	.progress-label {
		display: flex;
		justify-content: space-between;
		font-size: 0.75rem;
		color: #ae9caf;
		margin-bottom: 0.25rem;
	}

	.progress-bar-track {
		width: 100%;
		height: 6px;
		border-radius: 999px;
		background: rgba(31, 55, 37, 0.9);
		overflow: hidden;
	}

	.progress-bar-fill {
		height: 100%;
		border-radius: 999px;
		background: linear-gradient(to right, #22c55e, #a3e635);
		transition: width 150ms ease-out;
	}

	.reset-button {
		margin-top: 0.75rem;
		font-size: 0.75rem;
		color: #f97373;
		background: transparent;
		border: 1px solid rgba(248, 113, 113, 0.5);
		padding: 0.25rem 0.5rem;
		border-radius: 0.35rem;
		cursor: pointer;
	}

	.reset-button:hover {
		background: rgba(248, 113, 113, 0.1);
	}

	.section-detail {
		padding-left: 0.5rem;
	}

	.section-detail h2 {
		font-size: 1.1rem;
		margin: 0 0 0.25rem;
	}

	.section-detail p {
		font-size: 0.9rem;
		color: #ad9caf;
		margin: 0 0 0.75rem;
	}

	.note {
		width: 80vw;
	}

	.add-note {
		margin-top: 1.5rem;
	}

	.tasks,
	.notes {
		margin-top: 0.5rem;
	}

	.task,
	.note {
		display: flex;
		align-items: center;
		gap: 0.5rem;
		padding: 0.45rem 0.5rem;
		border-radius: 0.35rem;
		background: rgba(15, 42, 19, 0.8);
		border: 1px solid rgba(51, 85, 51, 0.8);
		margin-bottom: 0.3rem;
	}

	.task.done {
		opacity: 0.7;
		background: rgba(22, 163, 74, 0.08);
		border-color: rgba(34, 197, 94, 0.5);
	}

	.task label {
		display: flex;
		align-items: center;
		gap: 0.5rem;
		flex: 1;
		cursor: pointer;
		font-size: 0.9rem;
	}

	.task-title {
		flex: 1;
	}

	.task-title.done {
		text-decoration: line-through;
		color: #6d806b;
	}

	.task input[type='checkbox'] {
		width: 1rem;
		height: 1rem;
		accent-color: #22c55e;
	}

	.task-remove {
		border: none;
		background: transparent;
		color: #7e6b80;
		cursor: pointer;
		font-size: 0.85rem;
	}

	.task-remove:hover {
		color: #f97373;
	}

	.add-task {
		margin-top: 0.5rem;
		display: flex;
		gap: 0.4rem;
	}

	.add-task input,
	.add-note textarea,
	.add-note input {
		flex: 1;
		padding: 0.35rem 0.5rem;
		border-radius: 0.35rem;
		border: 1px solid rgba(55, 81, 57, 0.9);
		background: rgba(15, 42, 15, 0.9);
		color: #e5e7eb;
		font-size: 0.85rem;
	}

	.add-task button,
	.add-note button {
		padding: 0.35rem 0.6rem;
		border-radius: 0.35rem;
		border: none;
		background: #22c55e;
		color: #022c22;
		font-size: 0.85rem;
		cursor: pointer;
		font-weight: 500;
	}

	.add-task button:hover {
		background: #16a34a;
	}

	.hint {
		margin-top: 0.75rem;
		font-size: 0.8rem;
		color: #7d6b80;
	}

	@media print {
		main *,
		header,
		.hidden {
			visibility: hidden;
		}

		.notes-print-area,
		.notes-print-area * {
			visibility: visible;
		}

		.notes-print-area {
			position: absolute;
			top: 0;
			left: 0;
			width: 100%;
			padding: 1rem;
		}

		/* Optional: hide remove buttons in print */
		.note-remove {
			display: none !important;
		}
	}
</style>
