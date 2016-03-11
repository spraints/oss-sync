require "octokit"

desc "Use GITHUB_TOKEN to refresh the import of SOURCE_IMPORT_NWO*"
task :kick do
  kicker = Kicker.new
  begin
    puts "Logged in as #{kicker.gh.user.login}"
    ENV.each do |key, value|
      if key =~ /\ASOURCE_IMPORT_NWO/
        puts value
        begin
          kicker.kick(value)
          puts "... requeued."
        rescue Octokit::NotFound
          puts "... repository or import not found."
        end
      end
    end
  rescue Octokit::Unauthorized
    puts "$GITHUB_TOKEN must be set to a valid github personal auth token"
  end
end

class Kicker
  def kick(nwo)
    progress = gh.source_import_progress(nwo, opts)
    start_opts = opts.dup
    [:svn_root, :tfvc_project].each do |opt|
      if val = progress[opt]
        start_opts[opt] = val
      end
    end
    gh.start_source_import(nwo, progress.vcs, progress.vcs_url, start_opts)
  end

  def gh
    @gh ||= Octokit::Client.new access_token: ENV["GITHUB_TOKEN"]
  end

  def opts
    {:accept => Octokit::Preview::PREVIEW_TYPES.fetch(:source_imports)}
  end
end
