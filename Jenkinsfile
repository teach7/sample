/*
 * This TargetLink based example shows a common workflow:
 * - profile creation
 * - test case import
 * - test execution
 * - structural test generation
 * - b2b test TL MIL vs. SIL
 *
 * After each step the status is checked and the job is
 * stopped in case of errors.
 * 
 * Copyright (c) 2016-2018
 * BTC Embedded Systems AG, Oldenburg, Germany
 * All rights reserved
 */
node {
    stage ("Prep") {
        // JENKINS: fetch changes from SCM
        checkout scm

        // BTC: start EmbeddedPlatform and connect to it
        r = btc.startup {}
        if (r >= 300)
            throw new Exception("Step exited with code ${r}")
    }
 
    stage ("Profile Creation") {
        // BTC: load / create / update a profile
        r = btc.profileCreateTL {
            profilePath = "profileTL.epp"
            tlModelPath = "powerwindow_tl.slx"
            tlScriptPath = "start.m"
            matlabVersion = "2018b"
            licenseLocationString = "27001@OL8U3R2"
        }
    }
    
    stage ("Vector Import") {
        r = btc.vectorImport {
            importDir = "io"
            vectorFormat = "EXCEL"
        }
        if (r != 200)
            throw new Exception("Step exited with code ${r}")

        r = btc.vectorImport {
            importDir = "io"
            vectorFormat = "CSV"
        }
        if (r > 300)
            throw new Exception("Step exited with code ${r}")
    }
    
    stage ("Test Execution") {
        r = btc.rbtExecution {
            executionConfigString = "TL MIL"
        }
        if (r >= 300)
            throw new Exception("Step exited with code ${r}")
    }
    
    stage ("Vector Generation") {
        /*
			Note: Range Violation and Domain Coverage Goals
			      require the respective plugins to be installed
			r = btc.rangeViolationGoals {
			}
			if (r >= 500)
				throw new Exception("Step exited with code ${r}")
			r = btc.domainCoverageGoals {
				raster = "20"
			}
			if (r >= 500)
				throw new Exception("Step exited with code ${r}")
        */
        r = btc.vectorGeneration {
            pll = "MCDC; RO; DZ; CA"
        }
        if (r >= 500)
            throw new Exception("Step exited with code ${r}")
    }

    stage ("Back-to-Back Test") {
        r = btc.backToBack {
            reference = "TL MIL"
            comparison = "SIL"
            debugConfigString = "TL MIL"
        }
        if (r > 300)
            throw new Exception("Step exited with code ${r}")
    }

    stage ("Wrap Up") {    
        // BTC: close EmbeddedPlatform and store reports
        btc.wrapUp {}
    }
}