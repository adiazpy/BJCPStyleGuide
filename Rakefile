# -*- coding: utf-8 -*-
$:.unshift("/Library/RubyMotion/lib")
require 'motion/project/template/ios'

begin
  require 'bundler'
  Bundler.setup
  Bundler.require
rescue LoadError
end

Motion::Project::App.setup do |app|
  app.name = 'BJCPStyles'
  app.identifier = 'com.yourcompany.BJCPStyles' # I don't like it, but I inherited this app identifier.

  app.short_version = "3.1.0"
  app.version = (`git rev-list HEAD --count`.strip.to_i).to_s

  app.deployment_target = "7.0"

  app.device_family = [:iphone, :ipad]
  app.interface_orientations = [:portrait, :landscape_left, :landscape_right, :portrait_upside_down]

  app.frameworks += ["QuartzCore"]
  app.libs << "/usr/lib/libsqlite3.dylib"

  app.icons = Dir.glob("resources/Icon*.png").map{|icon| icon.split("/").last}

  app.info_plist['UIRequiresFullScreen'] = true
  app.info_plist['APP_STORE_ID'] = 293788663
  app.info_plist['CFBundleURLTypes'] = [
    { 'CFBundleURLName' => app.identifier,
      'CFBundleURLSchemes' => ['bjcpstyle', 'bjcp'] }
  ]
  app.info_plist["LSApplicationQueriesSchemes"] = ["beerjudge"]

  app.files_dependencies 'app/screens/detail_screen.rb' => 'app/screens/sizeable_web_screen.rb'
  app.files_dependencies 'app/screens/intro_screen.rb'  => 'app/screens/sizeable_web_screen.rb'

  app.pods do
    pod 'Appirater'
    pod 'OpenInChrome'
    pod 'EAIntroView', '~> 2.7.0'
    pod 'CrittercismSDK', '~> 5.2.0'
  end

  app.development do
    # We only want this cocoapod in development mode.
    app.pods do
      # pod "Reveal-iOS-SDK"
    end

    app.entitlements['get-task-allow'] = true
    app.codesign_certificate = "iPhone Developer: Mark Rickert (YA2VZGDX4S)"
    app.provisioning_profile = "./provisioning/development.mobileprovision"
  end

  app.release do
    app.entitlements['get-task-allow'] = false
    app.codesign_certificate = "iPhone Distribution: Mohawk Apps, LLC (DW9QQZR4ZL)"
    app.provisioning_profile = "./provisioning/release.mobileprovision"
  end

end

def build_path
  "build/iPhoneOS-7.0-Release/"
end

def dsym_name
  '"BJCPStyles.app.dSYM"'
end

after :"archive:distribution" do
  `rm #{build_path}#{dsym_name}.zip`
  sh "cd #{build_path} && zip -r build.dsym.zip #{dsym_name}"

  config = YAML.load_file("config/crittercism.yml")

  app_id = config['crittercism']['app_id']
  api_key = config['crittercism']['api_key']

  if app_id != "" && api_key != ""
    cmd = <<-CMD
      cd #{build_path} &&
      curl "https://api.crittercism.com/api_beta/dsym/#{app_id}"
        -F dsym=@"build.dsym.zip"
        -F key="#{api_key}"
    CMD

    puts cmd
    sh cmd.gsub("\n", "\\\n")
  else
    puts "Please fill out the app id and api key values in config/crittercism.yml"
  end
end
