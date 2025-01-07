    scripts:
      - name: Install dependencies
        script: |
          npm install
          cd ios
          pod install
          cd ..
      - name: Set up match
        script: |
          bundle exec fastlane match appstore
      - name: Build iOS
        script: |
          xcodebuild -workspace $XCODE_WORKSPACE -scheme $XCODE_SCHEME -sdk iphoneos -configuration AppStoreDistribution archive -archivePath $HOME/Library/Developer/Xcode/Archives/$(date +%Y-%m-%d)/$XCODE_SCHEME.xcarchive
      - name: Export IPA
        script: |
          xcodebuild -exportArchive -archivePath $HOME/Library/Developer/Xcode/Archives/$(date +%Y-%m-%d)/$XCODE_SCHEME.xcarchive -exportOptionsPlist ios/exportOptions.plist -exportPath $HOME/Library/Developer/Xcode/Archives/$(date +%Y-%m-%d)
      - name: Deploy to App Store
        script: |
          bundle exec fastlane pilot upload --ipa $HOME/Library/Developer/Xcode/Archives/$(date +%Y-%m-%d)/$XCODE_SCHEME.ipa
    artifacts:
      - $HOME/Library/Developer/Xcode/Archives/$(date +%Y-%m-%d)/*.ipa