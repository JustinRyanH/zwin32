# zwin32

My personal wrapper around windows API, I try to use as much as possible in `std.os.windows`, but sometimes it is not available.

Future me will might break the win32.zig into multiple logical sections (that will likely mirror MSDN's version).

Example `build.zig`
``` zig

const Builder = @import("std").build.Builder;

pub fn build(b: *Builder) void {
    const target = b.standardTargetOptions(.{});
    const mode = b.standardReleaseOptions();
    if (target.isWindows() and target.getAbi() == .msvc) {
        const exe = b.addExecutable("example", "src/main.zig");

        exe.setTarget(target);
        exe.setBuildMode(mode);

        exe.addPackagePath("zwin32", "lib/zwin32/win32.zig");

        exe.linkSystemLibrary("c");
        exe.linkSystemLibrary("gdi32");
        exe.linkSystemLibrary("user32");

        exe.install();

        const run_cmd = exe.run();
        run_cmd.step.dependOn(b.getInstallStep());

        const run_step = b.step("run", "Run the app");
        run_step.dependOn(&run_cmd.step);
    } else {
        @panic("This example Exlusively targets Windows MSVC");
    }
}

```